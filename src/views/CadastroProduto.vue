<template>
  <v-container fluid>
    <v-row>
      <v-col style="margin-left: 2vw; margin-top: 30px;" cols="12">
        <v-form @submit.prevent="cadastrarProduto">
          <v-container>
            <v-row>
              <v-col cols="12">
                <h2>Informações do Produto</h2>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="6">
                <v-text-field outlined dense v-model="produto.nome" label="Nome" required></v-text-field>
              </v-col>
              <v-col cols="6">
                <v-text-field outlined dense v-model="produto.descricao" label="Descrição" required></v-text-field>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="2">
                <v-select outlined dense v-model="produto.categoriaId" :items="categorias" label="Categoria" item-text="nome" item-value="categoriaId" required></v-select>
              </v-col>              
              <v-col cols="2">
                <v-text-field outlined dense v-model="produto.estoque" label="Estoque" type="number" required></v-text-field>
              </v-col>
              <v-col cols="2">
                <v-text-field outlined dense v-model="produto.preco" prefix="R$" label="Preço de compra" required />
              </v-col>
              <v-col cols="2">
                <v-text-field outlined dense v-model="produto.precoVenda" prefix="R$" label="Preço de venda" required />
              </v-col>
              <v-col cols="4">
                <v-text-field outlined dense v-model="produto.codigo" label="Código" type="number" required></v-text-field>
              </v-col>                
            </v-row>

            <v-row>
              <v-col cols="3">
                <v-text-field outlined dense v-model="produto.imagemUrl" label="URL da Imagem"></v-text-field>
              </v-col>
              <v-col cols="1">
                <v-btn color="primary" type="submit">{{ editandoProduto !== -1 ? 'Editar Produto' : 'Adicionar Produto' }}</v-btn>
              </v-col>
            </v-row>
          </v-container>
          <v-container>
            <v-row>
              <v-col cols="1">
                <v-btn color="primary" @click="cadastrarTodosProdutos">Cadastrar Todos</v-btn>
              </v-col>              
            </v-row>

          </v-container>
        </v-form>
      </v-col>

        <v-col style="margin-left: 6vw;" class="text-left" cols="2">
          <div>Lucro</div>
          {{ produto.precoVenda ? formatarMoeda(calcularLucro()) : `R$ 0,00` }}
        </v-col>
        <v-col class="text-left" cols="2">
          <div>Porcentagem de lucro</div>
          {{ produto.preco ? Number((calcularLucro() / produto.preco) * 100).toFixed(2) : 0 }}%
        </v-col>
          <v-col class="text-left" cols="8">
        </v-col>
        <v-col cols="12">
          <v-divider style="margin-left: 3vw;" class="divider-line"></v-divider>
        </v-col>        
      </v-row>
      <v-row>

      <v-col cols="12" class="text-center">
        <h2>Lista de Produtos</h2>
      </v-col>
      <v-col cols="12">
        <table class="table" style="width: 100%;margin-left: 50px;">
          <thead>
            <tr>
              <th class="text-left">Nome</th>
              <th class="text-left">Códogo</th>
              <th class="text-left">Descrição</th>
              <th class="text-left">Preço de compra</th>
              <th class="text-left">Preço de venda</th>
              <th class="text-left">Categoria</th>
              <th class="text-left">Estoque</th>
              <th class="text-left">Ações</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(produto, index) in produtosAdicionados" :key="index">
              <td class="text-left">{{ produto.nome }}</td>
              <td class="text-left">{{ produto.codigo }}</td>
              <td class="text-left">{{ produto.descricao }}</td>
              <td class="text-left">R$ {{ Number(formatarMoeda(produto.preco)).toFixed(2) }}</td>
              <td class="text-left">R$ {{ Number(formatarMoeda(produto.precoVenda)).toFixed(2) }}</td>
              <td class="text-left">{{ getCategoriaNome(produto.categoriaId) }}</td>
              <td class="text-left">{{ produto.estoque }}</td>
              <td class="text-left">
                <v-btn color="primary" dark @click="editarProduto(index)">Editar</v-btn>
                <v-btn color="red" dark @click="excluirProduto(index)" style="margin-left: 10px;">Excluir</v-btn>
              </td>          
            </tr>
          </tbody>
        </table>
      </v-col>


    </v-row>

  </v-container>
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';

export default {
  data() {
    return {
      produto: {
        nome: '',
        codigo: null,
        descricao: '',
        preco: null,
        precoVenda: null,
        dataCadastro: new Date(),
        imagemUrl: '',
        estoque: null,
        categoria: '',
        categoriaId: null,
      },
      categorias: [],
      produtosAdicionados: [], // Lista de produtos adicionados
      editandoProduto: -1, // Índice do produto em edição (-1 para nenhum)
    };
  },

  created() {
    // Carregue as categorias ao criar o componente
    this.carregarCategorias();
  },

  methods: {
    async carregarCategorias(pageNumber = 1, pageSize = 10) {
  try {
    const response = await axios.get(`${process.env.VUE_APP_API_BASE_URL}/Categorias`, {
      params: { pageNumber, pageSize }
    });

    // Extraia os dados e os metadados de paginação da resposta
    const { data, headers } = response;
    this.categorias = data;

    // Extraia os metadados de paginação do cabeçalho da resposta
    const paginationData = JSON.parse(headers['x-pagination']);
    console.log('Metadados de paginação:', paginationData);
    
    // Você pode querer armazenar os metadados de paginação para uso posterior
    this.paginationData = paginationData;
  } catch (error) {
    console.error('Erro ao carregar categorias:', error);
  }
},

    getCategoriaNome(categoriaId) {
      const categoria = this.categorias.find(c => c.categoriaId === categoriaId);
      return categoria ? categoria.nome : 'Categoria não encontrada';
    },    

    formatarMoeda(valor) {
      if (valor !== null && valor !== undefined) {
        return valor.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
      } else {
        return '';
      }
    },

    calcularLucro() {
      return this.produto.precoVenda - this.produto.preco;
    },

    async cadastrarProduto() {
      if (this.editandoProduto !== -1) {
        // Editar o produto em vez de adicioná-lo
        this.produtosAdicionados[this.editandoProduto] = { ...this.produto };
        this.editandoProduto = -1; // Sair do modo de edição
      } else {
        // Adicionar o produto à lista
        this.produtosAdicionados.push({ ...this.produto });
      }
      // Limpar o formulário
      this.produto = {
        nome: '',
        codigo: null,
        descricao: '',
        preco: null,
        precoVenda: null,
        dataCadastro: new Date(),
        imagemUrl: '',
        estoque: null,
        categoria: '',
        categoriaId: null,
      };
    },

    editarProduto(index) {
      // Carregar o produto para edição
      this.produto = { ...this.produtosAdicionados[index] };
      this.editandoProduto = index;
    },

    excluirProduto(index) {
      // Excluir o produto da lista
      this.produtosAdicionados.splice(index, 1);
    },

    async cadastrarTodosProdutos() {
      try {
        for (const produto of this.produtosAdicionados) {
          // Envie cada produto ao servidor para cadastro
          const response = await axios.post(`${process.env.VUE_APP_API_BASE_URL}/Produtos`, produto);
          if (response.status === 201) {
            // Se o cadastro for bem-sucedido, você pode adicionar código adicional aqui, se necessário.
          } else {
            Swal.fire('Erro', 'Erro ao cadastrar produto', 'error');
            return; // Se houver um erro, pare o processo de cadastro.
          }
        }
        Swal.fire('Sucesso', 'Todos os produtos cadastrados com sucesso', 'success');
        this.produtosAdicionados = []; // Limpe a lista após o cadastro bem-sucedido.
      } catch (error) {
        Swal.fire('Erro', 'Erro ao cadastrar produtos', 'error');
      }
    },
  },
};
</script>

<style scoped>
.divider-line {
  border-bottom: 1px solid rgb(209, 209, 209);
}
</style>
