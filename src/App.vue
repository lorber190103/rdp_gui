<script setup lang="ts">
  import axios from 'axios';
  import InputBar from './components/InputBar.vue';
  import ValuesDisplay from './components/ValuesDisplay.vue';
  import TypesDisplay from './components/TypesDisplay.vue';
  import SensorsDisplay from './components/SensorsDisplay.vue';
  import LocationsDisplay from './components/LocationsDisplay.vue';
  import { ValueType } from './scripts/value_type';
  import { Sensor } from './scripts/sensor';
  import { Location } from './scripts/location';
  import { Value } from './scripts/value';
</script>

<script lang="ts">
  /**
   * Interface for a generic command.
   */
  interface ICommand {
    execute(component: any, value: string): void;
  }

  /**
   * Default implementation of ICommand.
   */
  class Command implements ICommand {
    component: any;

    constructor(component: any) {
      this.component = component;
    }

    execute(value: string): void {
      console.log('Default execute method. Value:', value);
    }
  }

  /**
   * Command for filtering by type.
   */
  class TypeFilterCommand extends Command {
    execute(value: string): void {
      this.component.filter_type = this.component.getTypeId(value);
    }
  }

  /**
   * Command for filtering by start value.
   */
  class StartFilterCommand extends Command {
    execute(value: string): void {
      this.component.filter_start = value;
    }
  }

  /**
   * Command for filtering by end value.
   */
  class EndFilterCommand extends Command {
    execute(value: string): void {
      this.component.filter_end = value;
    }
  }

  /**
   * Factory class for creating command instances based on type.
   */
  class CommandFactory {
    /**
     * Builds and returns a command instance based on the provided type and component.
     */
    static buildCommand(type: string, component: any): ICommand | null {
      switch (type) {
        case 'type':
          return new TypeFilterCommand(component);
        case 'start':
          return new StartFilterCommand(component);
        case 'end':
          return new EndFilterCommand(component);
        default:
          return null;
      }
    }
  }

  /**
   * Static class for executing commands.
   */
  class Executor {
    /**
     * Executes a command based on the provided key, argument, and component.
     */
    static execute(key: string, arg: string, component: any): void {
      const command = CommandFactory.buildCommand(key, component);

      if (command) {
        command.execute(arg, component);
      } else {
        console.log('Ignoring command', key, arg);
      }
    }
  }

  export default {
    data() {
      return {
        values: new Array<Value>(),
        value_types: new Array<ValueType>(),
        sensors: new Array<Sensor>(),
        locations: new Array<Location>(),
        filter_start: '',
        filter_end: '',
        filter_type: '',
      };
    },
    mounted() {
      this.get_types();
      this.get_sensors();
      this.get_locations();
      this.get_values().then((data) => {
        this.values = data;
      });
    },
    methods: {
      getTypeId(type_name: string) {
        var return_value = '';
        for (var i = 0; i < this.value_types.length; i++) {
          if (this.value_types[i].type_name.toUpperCase() == type_name.toUpperCase()) {
            return_value = '' + this.value_types[i].id;
            console.log('Found matching type', this.value_types[i]);
          }
        }
        return return_value;
      },

      update_search(args: string[]) {
        console.log('New search arguments', args);

        this.filter_end = '';
        this.filter_start = '';
        this.filter_type = '';

        for (let i = 0; i < args.length; i++) {
          const command_and_args = args[i].split(':');
          if (command_and_args.length === 2) {
            const key = command_and_args[0];
            const value = command_and_args[1];

            Executor.execute(key, value, this);
          } else {
            console.log('Ignoring command', args[i]);
          }
        }

        this.get_values().then((result) => {
          this.values = result;
        });
      },

      get_types() {
        axios
          .get('/api/type/')
          .then((result) => {
            this.value_types = result.data;
          })
          .catch((error) => {
            console.error(error);
          });
      },

      get_sensors() {
        axios
          .get('/api/sensors/')
          .then((result) => {
            this.sensors = result.data;
          })
          .catch((error) => {
            console.error(error);
          });
      },

      get_locations() {
        axios
          .get('/api/locations/')
          .then((result) => {
            this.locations = result.data;
          })
          .catch((error) => {
            console.error(error);
          });
      },

      get_values() {
        const promise = new Promise<Value[]>((accept, reject) => {
          const url = '/api/value/';
          var params: { [key: string]: string } = {};
          if (this.filter_type != '') {
            params['type_id'] = this.filter_type;
          }
          if (this.filter_end != '') {
            params['end'] = this.filter_end;
          }
          if (this.filter_start != '') {
            params['start'] = this.filter_start;
          }
          console.log('Trying to get url', url);
          axios
            .get(url, { params: params })
            .then((result) => {
              accept(result.data);
            })
            .catch((error) => {
              console.error(error);
              reject(error);
            });
        });
        return promise;
      },
    },
  };
</script>

<template>
  <div class="container p-1">
    <h1 class="row">RDP</h1>
    <InputBar @search="update_search" />
    <TypesDisplay :value_types="value_types" @update_type="get_types" />
    <SensorsDisplay :sensors="sensors" @update_sensor="get_sensors"/>
    <LocationsDisplay :locations="locations" @update_location="get_locations"/>
    <ValuesDisplay :values="values" :value_types="value_types" :sensors="sensors" :locations="locations"/>
  </div>
</template>
