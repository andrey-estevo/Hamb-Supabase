# PRD - Aplicativo Hamburguer

## 1. Visao geral

O projeto e um aplicativo mobile de pedidos para uma hamburgueria, desenvolvido com Expo, React Native, Expo Router, NativeWind, Zustand e Supabase. O usuario pode navegar pelo cardapio, visualizar detalhes dos produtos, adicionar itens ao carrinho, informar endereco de entrega e finalizar um pedido.

## 2. Objetivo do produto

Permitir que clientes montem pedidos de forma simples e rapida pelo celular, reduzindo atrito entre escolher produtos, revisar o carrinho e enviar o pedido para armazenamento no backend.

## 3. Publico-alvo

- Clientes de hamburgueria que desejam pedir lanches, bebidas e sobremesas.
- Atendentes ou operadores que precisam receber pedidos registrados no Supabase.
- Pequenos negocios que precisam de um app simples de cardapio e pedido.

## 4. Plataformas suportadas

- Android via Expo.
- iOS via Expo.
- Web via `expo start --web`, desde que as dependencias e adaptacoes de React Native Web estejam funcionando.

## 5. Stack tecnica

- React Native `0.79.5`
- Expo `~53.0.20`
- Expo Router `~5.1.4`
- Zustand `^5.0.7`
- AsyncStorage para persistencia local do carrinho
- Supabase JS `^2.108.2`
- NativeWind e Tailwind CSS para estilos
- Jest com preset `jest-expo`

## 6. Funcionalidades existentes

### 6.1 Home e cardapio

O usuario acessa a tela inicial e ve:

- Titulo "Faca um pedido".
- Lista horizontal de categorias.
- Lista vertical de produtos agrupados por categoria.
- Indicador de quantidade de itens no carrinho quando houver itens.

Categorias atuais:

- Lanche do dia
- Promocoes
- Sobremesa
- Bebidas

### 6.2 Selecao por categoria

Ao tocar em uma categoria, o app:

- Atualiza a categoria selecionada.
- Rola a lista de secoes ate a categoria escolhida.

### 6.3 Detalhe do produto

Ao tocar em um produto, o usuario acessa a tela de detalhe com:

- Imagem principal.
- Nome.
- Preco formatado em Real brasileiro.
- Descricao.
- Ingredientes.
- Botao para adicionar ao pedido.
- Link para voltar ao cardapio.

Se o produto nao existir, o usuario e redirecionado para a home.

### 6.4 Carrinho

O carrinho permite:

- Exibir produtos adicionados.
- Exibir quantidade por produto.
- Calcular e exibir o total.
- Remover uma unidade de um produto mediante confirmacao.
- Exibir mensagem de carrinho vazio.
- Informar endereco de entrega.
- Enviar pedido.
- Voltar ao cardapio.

### 6.5 Regras de quantidade

- Ao adicionar um produto inexistente no carrinho, ele entra com quantidade `1`.
- Ao adicionar um produto ja existente, sua quantidade aumenta em `1`.
- Ao remover um produto com quantidade maior que `1`, sua quantidade diminui em `1`.
- Ao remover um produto com quantidade `1`, ele sai do carrinho.

### 6.6 Persistencia local

O carrinho e persistido localmente usando Zustand com `persist` e AsyncStorage na chave:

```txt
hamburguer:cart
```

### 6.7 Finalizacao de pedido

Ao enviar um pedido:

- O app valida se o endereco foi preenchido.
- Calcula o total bruto do carrinho.
- Cria um registro na tabela `orders` com `address` e `total_price`.
- Cria registros na tabela `order_items` com os produtos do carrinho.
- Exibe alerta de sucesso.
- Limpa o carrinho.
- Retorna para a tela anterior.

## 7. Requisitos funcionais

### RF01 - Listar cardapio

O sistema deve listar produtos agrupados por categoria na tela inicial.

Critérios de aceite:

- Deve exibir todas as categorias definidas em `MENU`.
- Deve exibir todos os produtos associados as suas categorias.
- Cada produto deve exibir imagem, titulo e descricao.

### RF02 - Navegar para detalhe do produto

O sistema deve permitir abrir o detalhe de um produto a partir da home.

Critérios de aceite:

- Ao selecionar um produto valido, deve abrir `/product/:id`.
- A tela deve exibir informacoes completas do produto.
- Se o `id` nao corresponder a um produto, deve redirecionar para `/`.

### RF03 - Adicionar produto ao carrinho

O sistema deve adicionar produtos ao carrinho a partir da tela de detalhe.

Critérios de aceite:

- Produto novo deve ser adicionado com quantidade `1`.
- Produto repetido deve incrementar quantidade.
- Apos adicionar, o app deve retornar para a tela anterior.

### RF04 - Exibir quantidade no cabecalho

O sistema deve exibir o atalho do carrinho quando houver itens.

Critérios de aceite:

- Com carrinho vazio, o icone do carrinho nao deve aparecer no cabecalho.
- Com itens no carrinho, o icone deve aparecer.
- O contador deve representar a soma das quantidades, nao apenas a quantidade de produtos distintos.

### RF05 - Revisar carrinho

O sistema deve permitir revisar itens antes de finalizar.

Critérios de aceite:

- Deve listar todos os produtos adicionados.
- Deve exibir quantidade por produto.
- Deve exibir total formatado em BRL.
- Carrinho vazio deve exibir mensagem adequada.

### RF06 - Remover item do carrinho

O sistema deve pedir confirmacao antes de remover uma unidade de um produto.

Critérios de aceite:

- Ao tocar em um item no carrinho, deve abrir alerta de confirmacao.
- Ao cancelar, o carrinho deve permanecer igual.
- Ao confirmar, deve remover uma unidade ou remover o produto se a quantidade chegar a zero.

### RF07 - Informar endereco

O sistema deve exigir endereco antes de salvar o pedido.

Critérios de aceite:

- Endereco vazio ou apenas com espacos deve bloquear a finalizacao.
- Deve exibir alerta solicitando o endereco.
- Endereco preenchido deve permitir tentativa de envio.

### RF08 - Salvar pedido no Supabase

O sistema deve salvar pedido e itens no Supabase.

Critérios de aceite:

- Deve inserir `address` e `total_price` na tabela `orders`.
- Deve inserir os itens na tabela `order_items`.
- Em sucesso, deve limpar o carrinho e voltar.
- Em erro ao salvar pedido, deve exibir alerta e nao tentar salvar itens.
- Em erro ao salvar itens, deve exibir alerta e preservar o carrinho.

## 8. Requisitos nao funcionais

- O app deve manter o carrinho mesmo apos fechar e abrir novamente.
- A interface deve ser responsiva para diferentes tamanhos de tela mobile.
- A formatacao monetaria deve seguir `pt-BR` e moeda `BRL`.
- Falhas de backend devem ser comunicadas por alerta.
- Variaveis sensiveis do Supabase devem ser lidas de variaveis de ambiente `EXPO_PUBLIC_SUPABASE_URL` e `EXPO_PUBLIC_SUPABASE_ANON_KEY`.

## 9. Fora de escopo atual

- Login ou cadastro de usuario.
- Pagamento online.
- Rastreamento do pedido.
- Painel administrativo.
- Edicao de quantidade com botoes `+` e `-`.
- Cupom de desconto.
- Taxa de entrega.
- Integracao com WhatsApp, apesar de existir uma constante `PHONE_NUMBER` no carrinho.

## 10. Riscos e observacoes

- A tela de carrinho possui textos com caracteres quebrados, indicando possivel problema de encoding.
- A funcao `handleOder` parece ter erro de nomeacao; o nome esperado seria `handleOrder`.
- A constante `PHONE_NUMBER` esta declarada mas nao e utilizada.
- O app registra URL e parte da chave do Supabase no console; isso deve ser removido ou limitado em producao.
- Atualmente nao ha configuracao explicita de bibliotecas de teste para interacoes de React Native, como `@testing-library/react-native`.

## 11. Possiveis testes unitarios

### 11.1 Utilitario `formatCurrency`

Arquivo sugerido:

```txt
src/utils/functions/format-currency.test.ts
```

Cenarios:

- Deve formatar `24.9` como moeda BRL em `pt-BR`.
- Deve formatar `0` corretamente.
- Deve formatar valores com centavos.
- Deve manter arredondamento padrao de `toLocaleString`.

### 11.2 Helper `cart-in-memory`

Arquivo sugerido:

```txt
src/stores/helpers/cart-in-memory.test.ts
```

Cenarios para `add`:

- Deve adicionar produto novo com quantidade `1`.
- Deve incrementar quantidade quando o produto ja existe.
- Deve preservar os demais produtos.
- Deve retornar um novo array sem mutar o estado original.

Cenarios para `remove`:

- Deve reduzir quantidade quando maior que `1`.
- Deve remover produto quando quantidade chegar a `0`.
- Deve preservar os demais produtos.
- Deve retornar lista inalterada ao remover id inexistente.
- Deve retornar array vazio ao remover o unico produto com quantidade `1`.

### 11.3 Store `useCartStore`

Arquivo sugerido:

```txt
src/stores/cart-store.test.ts
```

Cenarios:

- Estado inicial deve ter `products` vazio.
- `add` deve adicionar produto ao estado.
- `add` repetido deve incrementar quantidade.
- `remove` deve diminuir/remover produto.
- `clear` deve limpar todos os produtos.
- Deve usar a chave de persistencia `hamburguer:cart`.

Observacao: para este teste, mockar AsyncStorage ou usar setup do `jest-expo`.

### 11.4 Dados do cardapio

Arquivo sugerido:

```txt
src/utils/data/products.test.ts
```

Cenarios:

- `PRODUCTS` deve conter todos os produtos de `MENU`.
- `CATEGORIES` deve conter todos os titulos de `MENU`.
- Cada produto deve possuir `id`, `title`, `price`, `description`, `cover`, `thumbnail` e `ingredients`.
- IDs dos produtos devem ser unicos.
- Precos devem ser numeros positivos.

### 11.5 Componente `Header`

Arquivo sugerido:

```txt
src/components/header.test.tsx
```

Cenarios:

- Deve renderizar o titulo recebido.
- Nao deve renderizar atalho do carrinho quando `cartQuantityItem` for `0` ou indefinido.
- Deve renderizar contador quando `cartQuantityItem` for maior que `0`.
- Deve exibir o valor correto no contador.

### 11.6 Componente `Product`

Arquivo sugerido:

```txt
src/components/products.test.tsx
```

Cenarios:

- Deve renderizar titulo e descricao do produto.
- Deve renderizar imagem com `thumbnail`.
- Deve renderizar quantidade quando `quantity` existir.
- Nao deve renderizar quantidade quando `quantity` for indefinida.
- Deve chamar `onPress` ao tocar no produto.

### 11.7 Tela Home

Arquivo sugerido:

```txt
src/app/index.test.tsx
```

Cenarios:

- Deve renderizar o titulo "Faca um pedido".
- Deve renderizar categorias.
- Deve renderizar produtos do menu.
- Deve calcular quantidade total do carrinho somando `quantity`.
- Ao selecionar categoria, deve chamar `scrollToLocation` com a secao correta.

Observacao: mockar `useCartStore`, `expo-router` e refs da `SectionList` quando necessario.

### 11.8 Tela de detalhe do produto

Arquivo sugerido:

```txt
src/app/product/[id].test.tsx
```

Cenarios:

- Deve renderizar dados do produto quando `id` for valido.
- Deve renderizar ingredientes.
- Deve formatar preco em BRL.
- Deve adicionar produto ao carrinho ao tocar em "Adicionar ao pedido".
- Deve chamar `navigation.goBack` apos adicionar.
- Deve redirecionar para home quando `id` for invalido.

Observacao: mockar `useLocalSearchParams`, `useNavigation`, `Redirect` e `useCartStore`.

### 11.9 Tela Carrinho

Arquivo sugerido:

```txt
src/app/cart.test.tsx
```

Cenarios:

- Deve exibir mensagem de carrinho vazio.
- Deve listar produtos quando o carrinho tiver itens.
- Deve calcular total considerando `price * quantity`.
- Deve validar endereco vazio antes de enviar.
- Deve exibir alerta de confirmacao ao remover produto.
- Ao confirmar remocao, deve chamar `cartStore.remove`.
- Em sucesso no envio, deve inserir pedido, inserir itens, limpar carrinho e voltar.
- Em erro ao inserir pedido, deve exibir alerta e nao inserir itens.
- Em erro ao inserir itens, deve exibir alerta e nao limpar carrinho.

Observacao: mockar `supabase`, `Alert`, `useNavigation` e `useCartStore`.

## 12. Exemplo de estrutura inicial de testes

Dependencias recomendadas para testes de componentes:

```bash
npm install -D @testing-library/react-native @testing-library/jest-native
```

Possivel estrutura:

```txt
src/
  app/
    cart.test.tsx
    index.test.tsx
    product/
      [id].test.tsx
  components/
    header.test.tsx
    products.test.tsx
  stores/
    cart-store.test.ts
    helpers/
      cart-in-memory.test.ts
  utils/
    data/
      products.test.ts
    functions/
      format-currency.test.ts
```

## 13. Metricas de sucesso

- Usuario consegue adicionar produto ao carrinho em ate 2 toques apos abrir o detalhe.
- Usuario consegue finalizar pedido preenchendo apenas endereco.
- Carrinho persiste entre sessoes.
- Pedido e itens sao salvos no Supabase sem perda de informacao.
- Testes unitarios cobrem regras de carrinho, formatacao monetaria, renderizacao basica e fluxo de envio.

