<template>
<div>
    <!-- Page Content -->
    <div class="content">
        <!-- Full Table -->
        <base-block rounded title="Recipient Type">
            <template #options>
                <!-- Default Variation -->
                <base-block rounded>
                    <b-button variant="alt-primary" class="push" v-b-modal.modal-block-normal>
                        Add New Recipient
                    </b-button>
                </base-block>
                <!-- END Default Variation -->

                <!-- Normal Block Modal -->
                <b-modal id="modal-block-normal" body-class="p-0" hide-footer hide-header>
                    <div class="block block-rounded block-themed block-transparent mb-0">
                        <div class="block-header bg-primary-dark">
                            <h3 class="block-title">Add New Recipient</h3>
                            <div class="block-options">
                                <button type="button" class="btn-block-option" @click="$bvModal.hide('modal-block-normal')">
                                    <i class="fa fa-fw fa-times"></i>
                                </button>
                            </div>
                        </div>
                        <b-form class="block-content font-size-sm" @submit="onSubmit">
                            <b-form-group label="Recipient" label-for="Add new Recipient">
                                <b-form-input id="recipient" name="recipient" v-model="$v.form.label.$model" :state="!$v.form.label.$error && null" placeholder="Add new Recipient"></b-form-input>
                                <b-form-invalid-feedback id="label-feedback">
                                    Please enter Recipient
                                </b-form-invalid-feedback>
                            </b-form-group>
                            <b-form-group>
                                <b-button type="submit" size="sm" variant="primary">
                                    Submit
                                </b-button>
                            </b-form-group>
                        </b-form>
                    </div>
                </b-modal>
                <!-- END Normal Block Modal -->
            </template>
            <b-table-simple responsive bordered striped table-class="table-vcenter">
                <b-thead>
                    <b-tr>
                        <b-th>Recipients</b-th>
                        <b-th class="text-center" style="min-width: 110px; width: 110px;">Actions</b-th>
                    </b-tr>
                </b-thead>
                <b-tbody>
                    <b-tr v-for="recipient in recipients" :key="recipient.id">
                        <b-td class="font-w600 font-size-sm">
                            <a>
                                {{ recipient.label }}
                            </a>
                        </b-td>
                        <b-td class="text-center">
                            <b-btn-group>
                                <b-button size="md" variant="alt-primary" :to="`recipient/${recipient.id}`">
                                    <i class="fa fa-fw fa-pencil-alt"></i>
                                </b-button>
                                <b-button size="md" variant="alt-primary" @click.prevent="deleteItem(recipient.id)">
                                    <i class="fa fa-fw fa-times"></i>
                                </b-button>
                            </b-btn-group>
                        </b-td>
                    </b-tr>
                </b-tbody>
            </b-table-simple>
        </base-block>
        <!-- END Full Table -->

    </div>
    <!-- END Page Content -->
</div>
</template>

<script>
import {
    mapGetters
} from 'vuex';

import {
    validationMixin
} from 'vuelidate'

import {
    required,
    minLength
} from 'vuelidate/lib/validators'

export default {
    mixins: [validationMixin],
    data() {
        return {
            form: {
                label: null
            }
        }
    },
    validations: {
        form: {
            label: {
                required,
                minLength: minLength(3)
            }
        }
    },
    computed: mapGetters(['recipients']),
    methods: {
        onSubmit(evt) {
            evt.preventDefault()

            this.$v.form.$touch()

            if (this.$v.form.$anyError) {
                return
            }

            let data = {
                label: this.form.label,
            }

            this.$store
                .dispatch("addrecipient", data)
                .then(() => {
                    this.$bvModal.hide("modal-block-normal")
                    this.$store.dispatch("getrecipients")
                    this.form.label = ""
                })
                .catch(err => console.log(err))
        },
        deleteItem(id) {
            if (confirm('Are you sure you want to delete this recipient?')) {
                this.$store
                    .dispatch("deleterecipient", id)
                    .then(() => {
                        this.$store.dispatch("getrecipients")
                    })
                    .catch(err => console.log(err))
            }
        }
    },
    mounted() {
        this.$store.dispatch("getrecipients")
    }
}
</script>
