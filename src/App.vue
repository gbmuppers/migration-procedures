 <template>
  <div class="container">
    <div class="row">
        <div class="col-md-6">
            <form method="post">
                <h2 class="text-center">Procedimiento</h2>
                <div class="mb-3">
                  <span>Nombre</span>
                  <input v-model="procedimiento.nombre" type="text" class="form-control" />
                </div>
                <div class="mb-3">
                  <span>Clave</span>
                  <input v-model="procedimiento.clave" type="text" class="form-control" />
                </div>
                <div class="mb-3">
                  <span>Contenido escrito</span>
                  <editor
                    v-model="procedimiento.contenido_escrito"
                    api-key="nzr8mbed3t42emvyd6kaqzee5j4ouqtspw1fakb0j1aqk3mh"
                    :init="{
                          height: 250,
                          menubar: false,
                          plugins: [
                          'preview paste searchreplace autolink directionality code visualblocks visualchars fullscreen link codesample table charmap hr pagebreak nonbreaking anchor toc insertdatetime advlist lists wordcount textpattern noneditable help quickbars emoticons'
                          ],
                          toolbar:
                          'undo redo | fullscreen preview | bold italic underline strikethrough | link codesample | fontselect fontsizeselect formatselect | alignleft aligncenter alignright alignjustify | outdent indent |  numlist bullist | forecolor backcolor removeformat | pagebreak | charmap emoticons | ltr rtl',
                          quickbars_insert_toolbar: 'link',
                          toolbar_sticky: true,
                      }"
                  />
                </div>
                <div class="mb-3">
                  <span>¿A quién va dirigido?</span>
                  <select v-model="procedimiento.dirigido_a" class="form-select" multiple aria-label="multiple select example">
                        <option v-for="(opcion,index) in opciones_puestos" :key="index" :value="opcion.id_puesto">{{ opcion.puesto }}</option>
                  </select>
                </div>
            </form>
        </div>
        <div class="col-md-6 py-5">
            <div class="mb-3">
              <button class="btn btn-info link-light float-end" type="button" v-clipboard:copy="output_model_type" v-clipboard:success="onCopy" v-clipboard:error="onError">Copiar <i class="fas fa-paperclip"></i></button>
              <span class="text-secondary">Salida tipo Model</span>
              <textarea v-model="output_model_type" class="d-block text-left rounded shadow-lg mt-3 py-3 px-3" style="width:100%;height:10rem;overflow-y:auto;autoresize:none;" readonly></textarea>
            </div>
            <div class="mb-3">
              <button class="btn btn-info link-light float-end" type="button" v-clipboard:copy="output_sql_type" v-clipboard:success="onCopy" v-clipboard:error="onError">Copiar <i class="fas fa-paperclip"></i></button>
              <span class="text-secondary">Salida tipo SQL</span>
              <textarea v-model="output_sql_type" class="d-block text-left rounded shadow-lg mt-3 py-3 px-3" style="width:100%;height:10rem;overflow-y:auto;autoresize:none;" readonly></textarea>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
  import Editor from '@tinymce/tinymce-vue'

  export default {
    name: 'App',
    components: {
      'editor': Editor
    },
    data(){
      return{
        output_model_type: '',
        output_sql_type: '',
        opciones_puestos:[
          {"id_puesto":1,"puesto":"Administrador del sistema"},
          {"id_puesto":2,"puesto":"Junta directiva"},
          {"id_puesto":3,"puesto":"Director de experiencias"},
          {"id_puesto":5,"puesto":"Recursos humanos"},
          {"id_puesto":6,"puesto":"Mercadotecnia"},
          {"id_puesto":7,"puesto":"Auxiliar de mercadotecnia"},
          {"id_puesto":8,"puesto":"Caja general"},
          {"id_puesto":9,"puesto":"Auxiliar de caja general"},
          {"id_puesto":10,"puesto":"Inventarios"},
          {"id_puesto":11,"puesto":"Auxiliar de inventarios"},
          {"id_puesto":12,"puesto":"Almacén accesorios"},
          {"id_puesto":13,"puesto":"Auxiliar de almacén accesorios"},
          {"id_puesto":14,"puesto":"Contabilidad"},
          {"id_puesto":15,"puesto":"Auxiliar de contabilidad"},
          {"id_puesto":16,"puesto":"Almacén telefonía"},
          {"id_puesto":17,"puesto":"Dirección de contenidos"},
          {"id_puesto":18,"puesto":"Jefe de sistemas"},
          {"id_puesto":19,"puesto":"Almacén técnico"},
          {"id_puesto":20,"puesto":"Almacén baterias"},
          {"id_puesto":21,"puesto":"Lider de experiencia"},
          {"id_puesto":22,"puesto":"Técnico"},
          {"id_puesto":23,"puesto":"Cajero"},
          {"id_puesto":24,"puesto":"Vendedor"}
        ],
        procedimiento:{
          nombre: '',
          clave: '',
          contenido_escrito: '',
          dirigido_a: [],
        }
      }
    },
    watch:{
      procedimiento:{
        type: Object,
        handler () {
          
          this.output_model_type = "\\App\\Models\\Procedimiento::create(['titulo' =>'"+this.procedimiento.nombre+"', 'clave' =>'"+this.procedimiento.clave+"','contenido' =>'"+this.procedimiento.contenido_escrito.replaceAll('\n','\\n')+"']);";
          var latest = "$procedimiento = \\App\\Models\\Procedimiento::latest()->first();";;
          var attach = "$procedimiento->puesto()->attach(["+this.procedimiento.dirigido_a+"]);";
          this.output_model_type+="\n"+latest+"\n"+attach;
          
          this.output_sql_type = this.procedimiento.nombre + this.procedimiento.clave + " SQL";
        },
        deep:true,
      },
    },
    methods:{
      onCopy: function (e) {
        Swal.fire({
          position: 'bottom-end',
          icon: 'success',
          title: 'Copiado en el portapapeles',
          showConfirmButton: false,
          timer: 1500
        })
        //alert('You just copied the following text to the clipboard: ' + e.text)
      },
      onError: function (e) {
        Swal.fire({
          position: 'bottom-end',
          icon: 'error',
          title: 'Ocurrió un error, mi pana.',
          showConfirmButton: false,
          timer: 1500
        })
          //alert('Failed to copy the text to the clipboard')
          //console.log(e);
      }
    }
  }
</script>