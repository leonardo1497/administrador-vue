<template>
  <div style="margin:5%">
    <v-card>
    <v-card-title>Historial de compras</v-card-title></v-card>
    <v-data-table :headers="headers" :items="elements" class="elevation-1">
      <template v-slot:items="props">
        <td>{{ props.item.nombre }}</td>
        <td class="text-xs-left">{{ props.item.pizzaNombre }}</td>
        <td class="text-xs-left">{{ props.item.correo }}</td>
        <td class="text-xs-left">{{ props.item.direccion }}</td>
        <td class="text-xs-left">{{ props.item.telefono }}</td>
        <td class="text-xs-left">$ {{ props.item.totalPago }}</td>
        <td class="text-xs-left">{{ props.item.fecha }}</td>
        <td class="text-xs-left" v-if="props.item.estado == '1'"><v-btn   @click="notificarCliente(props.item.id,props.item.token,props.item.estado)">En proceso</v-btn></td>
        <td class="text-xs-left" v-if="props.item.estado == '2'"><v-btn  @click="notificarCliente(props.item.id,props.item.token,props.item.estado)">En entrega</v-btn></td>
        <td class="text-xs-left" v-if="props.item.estado == '3'"><v-btn  @click="notificarCliente(props.item.id,props.item.token,props.item.estado)">Entregado</v-btn></td>
        <td class="text-xs-left" v-if="props.item.estado == '4'"><v-btn  >Finalizado</v-btn></td>
      </template>
    </v-data-table>
  </div>
</template>

<script >
  export default {
   data: ()=>({
     boton:true,
     boton2: false,
     boton3: false,
     elements: [],
      headers: [  
         {text:'Comprador',value:'nombre'},
         { text: 'Pizza', value: 'pizza' },
        { text: 'Correo', value: 'correo' },
        { text: 'Dirección', value: 'direccion' },
        { text: 'Teléfono', value: 'telefono' },
        { text: 'Total de pago', value: 'totalPago' },
        { text: 'Fecha', value: 'fecha' },
        {text: 'Estado', value: 'estado'}
      ],
    }),
    created () {
      this.getElements()
    },
    methods: {
      getElements () {
      this.$axios.get(`https://api-pizza-adonis.herokuapp.com/api/v1/ordenes`)
      .then(response => {
        console.log(response.data)
        this.elements = response.data
      })
      .catch( e => {
        console.log(e)
      })
    },
    notificarCliente(id,token,estado){
      let newEstado;

      let body;
      if(estado=="1"){
        body="Tu pizza esta en proceso de realización"
        newEstado="2"
      }else if(estado=="2"){
        body="Tu pizza esta rumbo a su entrega"
        newEstado="3"
      }else if(estado=="3"){
        body="Tu pizza ha sido entregada exitosamente"
        newEstado="4"
      }
      let notification = {
        "title": "Notificación pizza",
        "body": body,
        "icon": "./img/icons/android-chrome-192x192.png"
      }
      let data = {
        "to": token,
        "notification": notification
      }
      this.$axios.post('https://fcm.googleapis.com/fcm/send',data,{
        headers:{
          'Authorization': "key=AAAAha0ESNM:APA91bGiMuE_dGDCW0nHcuEE4fD-T0qArbtOlyYqvu2cRhMmg58lMLqriu0EIUcBIqY3OD0q_EQUBLpqYEsm4ClPEbYKENe0jYL-abQ5DvOpQ0vmUDG1OjQGs2j1-_E9wtjtaJ8YklBL",
          'Content-Type': "application/json"

        },

        
        }).then((reponse)=>{
          console.log(reponse)
        }).catch( (e) => {
          console.log(e)
        })
        this.$axios.put(`https://api-pizza-adonis.herokuapp.com/api/v1/ordenes/`+id,{
          estado: newEstado,
      

        }).then((reponse)=>{
          console.log(reponse);
          this.getElements();
         
        }).catch( (e) => {
          console.log(e)
        })
    }
    }
  }
</script>
