<template>
    <div v-if="show">
        <div class="modal fade show display" v-cloak tabindex="-1" role="dialog" aria-labelledby="AddAppointmentModal" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-event_name">New Appointment</h5>
                        <button type="button" class="close" @click="closeModal" aria-label="Close">
                            <span aria-hidden="true">×</span>
                        </button>
                    </div>
    
                    <div class="p-2">
                        <ul class="list-group list-group-flush">
                            <li class="list-group-item">
                                <div class="input-group input-group-seamless">
                                    <div class="input-group-prepend">
                                        <div class="input-group-text">
                                            <i class="material-icons">edit</i>
                                        </div>
                                    </div>
                                    <input type="text" class="form-control w-100 modal-event_name" v-model="event.event_name" placeholder="Add event_name ..." ref="eventevent_name" autofocus>
                                </div>
                            </li>
                            <li class="list-group-item">
                                <i class="material-icons">event</i>
                                {{ formatDate(date.start) }}
                            </li>
                            <!-- <li class="list-group-item">
                                <div class="input-group input-group-seamless mb-3">
                                    <div class="input-group-prepend">
                                        <label class="input-group-text" for="userSelect">
                                            <i class="material-icons">assignment_ind</i>
                                        </label>
                                    </div>
                                    <select class="custom-select" id="userSelect" v-model="event.assignee">
                                        <option disabled selected value="nobody">
                                            Assign to:
                                        </option>
                                        <option v-for="user in users" :key="user.id" :value="user.id">
                                            {{ user.full_name }}
                                        </option>
                                    </select>
                                </div>
                            </li> -->
                            <!-- <li class="list-group-item">
                                <div class="form-group">
                                    <textarea class="form-control" id="appointmentNote" rows="3" v-model="event.note" placeholder="Description ...">
                                    </textarea>
                                </div>
                            </li> -->
                        </ul>
                    </div>
    
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" @click="closeModal" data-dismiss="modal">
                            Annulla
                        </button>
                        <button type="button" class="btn btn-primary" @click="saveEvent" :disabled="!validEventData">
                            Salva
                        </button>
                    </div>
    
                </div>
            </div>
        </div>
        <div v-cloak class="modal-backdrop fade show custom-modal-backdrop"></div>
    </div></template>
    
    <script>
    export default{
        props: ['show', 'date'],
            data: () => ({
                event: {
                    event_name: null,
                    // assignee: 'nobody',
                    // note: null
                },
            }),
    
            methods: {
                closeModal() {
                    this.event.event_name = null
                    // this.event.assignee = 'nobody'
                    // this.event.note = null
                    this.$emit('close')
                },
    
                formatDate(date, format = 'DD/MM/YY HH:mm') {
                    return moment.utc(date).format(format)
                },
    
                transformEventDates(start, end) {
                    // if start is same as end add 1hr
                    let startTime = new Date(start)
                    let endTime = new Date(end)
    
                    if (startTime.getTime() === endTime.getTime()) {
                        let endTime = (new Date(end))
                        endTime.setHours(endTime.getHours() + 1)
                        return {
                            start,
                            end: endTime.toISOString()
                        }
                    }
                    return {
                        start,
                        end
                    }
                },
    
                saveEvent() {
                    let eventData = this.transformEventDates(this.date.start, this.date.end)
                    let newEventData = {
                        start: eventData.start,
                        end: eventData.end,
                        event_name: this.event.event_name,
                        // assignee: this.event.assignee,
                        // note: this.event.note
                    }
    
                    this.$api.appointments.create(newEventData)
                        .then(({
                            data
                        }) => {
                            this.closeModal()
                            this.$emit('event-created')
                        })
                        .catch(error => {
                            this.$emit('error')
                        })
                }
    
            },
    
            // computed: {
            //     validEventData() {
            //         return !!(this.event.event_name && this.event.assignee != 'nobody')
            //     }
            // },
    
            // mounted() {
            //     // I absctracted my API calls, this would be the same as:
            //     // axios.get('/users').then( .... ) ...
            //     this.$api.users.index()
            //         .then(({
            //             data
            //         }) => {
            //             this.users = data
            //         })
            //         .catch(error => {
            //             this.users = []
            //             this.event.assignee = null
            //         })
            // }
        }
    </script>