<template>
  <div>
    <q-form greedy ref="formPessoa" @submit="cadastrarOuAtualizar" class="bg">
      <div class="bg"></div>
      <div class="main-container">
        <div class="q-mb-md">
          <h4 class="title">{{ pessoa.id ? 'Editar ' : 'Cadastrar ' }} Pessoa</h4>
          <div class="divisor-inline"></div>
        </div>
        <div class="q-pa-md" style="margin-top: -30px">
          <h4 class="subTitulo">Dados Básicos</h4>
          <div class="row q-col-gutter-lg">
            <div class="col-7">
              <q-input
                :rules="[vRequired]"
                v-model="pessoa.nome"
                label="Nome" dense />
            </div>
            <div class="col-2">
              <q-input :rules="[vRequired]"
                v-model="pessoa.documento" label="Documento" dense />
            </div>
            <div class="col-3">
              <q-input :rules="[vRequired]"
                v-model="pessoa.profissao" label="Profissão" dense />
            </div>
          </div>
          <div class="row q-col-gutter-lg" style="margin-top: -15px">
            <div class="col-7">
              <q-field ref="tipoPessoa" dense :model-value="pessoa.tipo" :rules="[vRequired]"
              label="Tipo de Pessoa" lazy-rules borderless stack-label>
                <template v-slot:control>
                  <q-option-group v-model="pessoa.tipo" :options="optionsTipoPessoa" type="radio" size="xs" inline />
                </template>
              </q-field>
            </div>
            <div class="col-5">
              <q-field dense ref="estadoCivil" :rules="[vRequired]" :model-value="pessoa.estadoCivil"
                label="Estado Civil" lazy-rules borderless stack-label>
                <template v-slot:control>
                  <q-option-group v-model="pessoa.estadoCivil" :options="optionsEstadoCivil" type="radio" size="xs"
                     inline />
                </template>
              </q-field>
            </div>
          </div>
        </div>
        <div class="q-pa-md" style="margin-top: -30px">
          <h4 class="subTitulo">Endereço</h4>
          <div class="row q-col-gutter-lg">
            <div class="col-2">
              <q-input :rules="[vRequired]" v-model="pessoa.endereco.cep"
                label="CEP" dense />
            </div>
            <div class="col-3">
              <q-input :rules="[vRequired]"
                v-model="pessoa.endereco.estado" label="Estado" dense />
            </div>
            <div class="col-4">
              <q-input :rules="[vRequired]"
                v-model="pessoa.endereco.cidade" label="Cidade" dense />
            </div>
            <div class="col-3">
              <q-input :rules="[vRequired]"
                v-model="pessoa.endereco.bairro" label="Bairro" dense />
            </div>
            <div class="col-9">
              <q-input :rules="[vRequired]"
                v-model="pessoa.endereco.rua" label="Rua" dense />
            </div>
            <div class="col-3">
              <q-input :rules="[vRequired]"
                v-model="pessoa.endereco.numero" label="Número" dense />
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-12">
            <div style="float: right">
              <q-btn @click="voltar" style="margin-right: 10px;" label="Voltar" no-caps class="btn-voltar" />
              <q-btn type="submit" :label="pessoa.id ? 'Salvar' : 'Cadastrar'" no-caps class="btn-cadastrar" />
            </div>
          </div>
        </div>
      </div>
    </q-form>
  </div>

</template>

<script>
import { ref } from 'vue'
import { pessoaService } from 'src/services/sgci-api-service.js'
import { useQuasar } from 'quasar'

export default {
  name: 'CreateEditPessoa',
  setup () {
    const pessoa = ref({
      id: null,
      nome: null,
      documento: null,
      profissao: null,
      tipo: ref(null),
      estadoCivil: ref(null),
      endereco: ref({
        cep: null,
        estado: null,
        cidade: null,
        rua: null,
        bairro: null,
        numero: null
      })
    })
    const $q = useQuasar()
    return {
      $q,
      pessoa,
      optionsTipoPessoa: [
        {
          label: 'Pessoa Física',
          value: 'PESSOA_FISICA'
        },
        {
          label: 'Pessoa Jurídica',
          value: 'PESSOA_JURIDICA'
        }
      ],
      optionsEstadoCivil: [
        {
          label: 'Casado',
          value: 'CASADO'
        },
        {
          label: 'Solteiro',
          value: 'SOLTEIRO'
        },
        {
          label: 'Divorciado',
          value: 'DIVORCIADO'
        }
      ]
    }
  },
  watch: {
    'pessoa.tipo': {
      handler () {
        this.$refs.tipoPessoa.resetValidation()
      }
    },
    'pessoa.estadoCivil': {
      handler () {
        this.$refs.estadoCivil.resetValidation()
      }
    }
  },
  mounted () {
    this.buscarPessoaParaEdicao()
  },
  methods: {
    buscarPessoaParaEdicao () {
      if (!this.$route.params.id) return
      pessoaService.getById(this.$route.params.id).then(retorno => {
        this.pessoa = retorno.data
      })
    },
    cadastrarOuAtualizar () {
      if (this.pessoa.id) {
        pessoaService.update(this.pessoa.id, this.pessoa).then(response => {
          this.$q.notify({ message: 'Registro editado com sucesso!', color: 'positive', textColor: 'white' })
          this.voltar()
        }).catch(error => {
          console.log(error)
        })
      } else {
        pessoaService.create(this.pessoa).then(response => {
          this.$q.notify({ message: 'Registro cadastrado com sucesso!', color: 'positive', textColor: 'white' })
          this.voltar()
        }).catch(error => {
          console.log(error)
        })
      }
    },
    voltar () {
      this.$router.push('/pessoas')
    }
  }
}

</script>
