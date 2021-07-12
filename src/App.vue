 <template>
  <div class="container">
    <div class="row">
        <div class="col-md-6 mt-2">
            <form method="post">
                <button @click="clear" class="btn btn-danger link-light float-end" type="button">Limpiar fomulario <i class="fas fa-eraser"></i></button>
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
                          'preview paste searchreplace autolink directionality code visualblocks visualchars fullscreen link codesample table charmap hr pagebreak nonbreaking anchor toc insertdatetime advlist lists wordcount textpattern noneditable help quickbars emoticons table'
                          ],
                          toolbar:
                          'undo redo | fullscreen preview | bold italic underline strikethrough | link codesample | fontselect fontsizeselect formatselect | alignleft aligncenter alignright alignjustify | table tabledelete | tableprops tablerowprops tablecellprops | tableinsertrowbefore tableinsertrowafter tabledeleterow | tableinsertcolbefore tableinsertcolafter tabledeletecol | outdent indent |  numlist bullist | forecolor backcolor removeformat | pagebreak | charmap emoticons | ltr rtl',
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
        <div class="col-md-6 py-5 mt-2">
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
      'editor': Editor,
    },
    data(){
      return{
        output_model_type: '',
        output_sql_type: '',
        opciones_puestos:[{"id_puesto":1,"puesto":"Administrador del sistema"},{"id_puesto":2,"puesto":"Junta directiva"},{"id_puesto":3,"puesto":"Director de experiencias"},{"id_puesto":5,"puesto":"Jefe de contenidos"},{"id_puesto":6,"puesto":"Jefe de recursos humanos"},{"id_puesto":7,"puesto":"Jefe de contabilidad"},{"id_puesto":8,"puesto":"Jefe de sistemas"},{"id_puesto":9,"puesto":"Jefe de almacén"},{"id_puesto":10,"puesto":"Jefe de finanzas"},{"id_puesto":11,"puesto":"Jefe de inventarios"},{"id_puesto":12,"puesto":"Jefe de telefonía"},{"id_puesto":13,"puesto":"Jefe del área técnica"},{"id_puesto":14,"puesto":"Jefe de Mantenimiento"},{"id_puesto":15,"puesto":"Jefe de mercadotecnia"},{"id_puesto":16,"puesto":"Jefe de almacén baterias"},{"id_puesto":17,"puesto":"Auxiliar de contabilidad"},{"id_puesto":18,"puesto":"Auxiliar de almacén"},{"id_puesto":19,"puesto":"Auxiliar de finanzas"},{"id_puesto":20,"puesto":"Auxiliar de inventarios"},{"id_puesto":21,"puesto":"Auxiliar de mercadotecnia"},{"id_puesto":22,"puesto":"Lider de experiencia"},{"id_puesto":23,"puesto":"Recepción de equipos"},{"id_puesto":24,"puesto":"Técnico nivel 1"},{"id_puesto":25,"puesto":"Técnico nivel 2"},{"id_puesto":26,"puesto":"Técnico nivel 3"},{"id_puesto":27,"puesto":"Técnico express"},{"id_puesto":28,"puesto":"Apasionado de la telefonía"},{"id_puesto":29,"puesto":"Cajero"},{"id_puesto":30,"puesto":"Traslado de valores"}],
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
          this.output_model_type = "\\App\\Models\\Procedimiento::create(['titulo' =>'"+this.procedimiento.nombre+"', 'clave' =>'"+this.procedimiento.clave+"','contenido' =>'"+this.procedimiento.contenido_escrito.replaceAll('\'','\\\'')+"']);"; //.replaceAll('\n','\\n')
          var latest = "$procedimiento = \\App\\Models\\Procedimiento::latest()->first();";;
          var attach = "$procedimiento->puesto()->attach(["+this.procedimiento.dirigido_a+"]);";
          this.output_model_type+="\n"+latest+"\n"+attach;


          var insert = "INSERT INTO procedimientos (clave, titulo, contenido) VALUES ('"+this.procedimiento.clave+"','"+this.procedimiento.nombre+"','"+this.procedimiento.contenido_escrito.replaceAll('\'','\\\'')+"');\n";
          var foreign = ""
          this.procedimiento.dirigido_a.forEach(value => foreign += "INSERT INTO procedimiento_puesto VALUES ((SELECT id_procedimiento FROM procedimientos ORDER BY id_procedimiento DESC LIMIT 1),"+value+");\n");

          this.output_sql_type = insert+foreign;
        },
        deep:true,
      },
    },
    methods:{
      onCopy: function () {
        Swal.fire({
          position: 'bottom-end',
          icon: 'success',
          title: 'Copiado en el portapapeles',
          showConfirmButton: false,
          timer: 1500
        })
        //alert('You just copied the following text to the clipboard: ' + e.text)
      },
      onError: function () {
        Swal.fire({
          position: 'bottom-end',
          icon: 'error',
          title: 'Ocurrió un error, mi pana.',
          showConfirmButton: false,
          timer: 1500
        })
      },
      clear(){
        this.procedimiento.nombre = ''
        this.procedimiento.clave = ''
        this.procedimiento.contenido_escrito = ''
        this.procedimiento.dirigido_a = []
        this.output_model_type = ''
        this.output_sql_type = ''
      }
    }
  }
</script>