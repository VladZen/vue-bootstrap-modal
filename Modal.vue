<!-- Taken from https://github.com/Coffcer/vue-bootstrap-modal -->
<template>
  <transition name="modal">
    <div v-show="show">
      <div class="modal"
           @click.self="clickMask">

        <div class="modal-dialog"
             :class="modalClass">
          <div class="modal-content">
            <!--Header-->
            <div class="modal-header">

              <slot name="header">
                <a type="button"
                   class="close"
                   @click="cancel">
                  <i class="fa fa-fw fa-times"></i>
                </a>
                <h4 class="modal-title">
                  <slot name="title">
                    {{ title }}
                  </slot>
                </h4>
              </slot>
            </div>

            <!--Container-->
            <div class="modal-body">
              <slot></slot>
            </div>

            <!--Footer-->
            <div class="modal-footer">
              <slot name="footer">
                <button type="button"
                        :class="cancelClass"
                        @click="cancel">
                  {{ cancelText }}
                </button>

                <button type="button"
                        :class="okClass"
                        @click="ok">
                  {{ okText }}
                </button>
              </slot>
            </div>
          </div>
        </div>
      </div>

      <div class="modal-backdrop in"></div>
    </div>
  </transition>
</template>

<script>
  /**
   * Bootstrap Style Modal Component for Vue
   * Depend on Bootstrap.css
   */
  export default {
    props: {
      show: {
        type: Boolean,
        twoWay: true,
        default: false
      },
      title: {
        type: String,
        default: 'Modal'
      },
      small: {
        type: Boolean,
        default: false
      },
      large: {
        type: Boolean,
        default: false
      },
      full: {
        type: Boolean,
        default: false
      },
      force: {
        type: Boolean,
        default: false
      },
      okText: {
        type: String,
        default: 'OK'
      },
      cancelText: {
        type: String,
        default: 'Cancel'
      },
      okClass: {
        type: String,
        default: 'btn btn-primary'
      },
      cancelClass: {
        type: String,
        default: 'btn red btn-outline'
      },
      closeWhenOK: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        duration: null
      };
    },
    computed: {
      modalClass () {
        return {
          'modal-lg': this.large,
          'modal-sm': this.small,
          'modal-full': this.full
        }
      }
    },
    created () {
      if (this.show) {
        document.body.className += ' modal-open';
      }
    },
    beforeDestroy () {
      document.body.className = document.body.className.replace(/\s?modal-open/, '');
    },
    watch: {
      show (value) {
        if (value) {
          document.body.className += ' modal-open';
        }
        else {
          if (!this.duration) {
            this.duration = window.getComputedStyle(this.$el)['transition-duration'].replace('s', '') * 1000;
          }
          window.setTimeout(() => {
            document.body.className = document.body.className.replace(/\s?modal-open/, '');
          }, this.duration || 0);
        }
      }
    },
    methods: {
      ok () {
        this.$emit('ok');
        if (this.closeWhenOK) {
          this.show = false;
        }
      },
      cancel () {
        this.$emit('cancel');
        this.show = false;
      },
      clickMask () {
        if (!this.force) {
          this.cancel();
        }
      }
    }
  };
</script>

<style scoped lang="scss">
.modal {
  display: block;
}

.modal-dialog {
  margin: 30% auto 30px;
}

.modal-enter-active, .modal-leave-active {
  transition: all .5s ease;

  .modal-dialog, .modal-backdrop {
    transition: all .5s ease;
  }
}

.modal-enter, .modal-leave-to {
  .modal-dialog {
    opacity: 0;
    transform: translateY(-40%);
  }

  .modal-backdrop {
    opacity: 0;
  }
}

.modal-leave, .modal-enter-to {
  .modal-dialog {
    opacity: 1;
    transform: translateY(0%);
  }

  .modal-backdrop {
    opacity: .5;
  }
}

.close {
  font-size: 16px;
}
</style>
