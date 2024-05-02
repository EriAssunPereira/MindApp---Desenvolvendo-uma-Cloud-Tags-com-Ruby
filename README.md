# MindApp---Desenvolvendo-uma-Cloud-Tags-com-Ruby

Visão geral e exemplos práticos para o desenvolvimento de uma **Cloud Tags** usando Ruby on Rails. Vamos dividir o projeto em módulos para uma melhor organização e manutenção do código.

### Descrição do Projeto:
O projeto **MindApp** é uma aplicação de **Cloud Tags**, que permite aos usuários criar e gerenciar uma nuvem de tags, que são palavras-chave ou termos que ajudam na classificação e no agrupamento de conteúdos. A aplicação será desenvolvida do início ao fim, utilizando o framework **Ruby on Rails**.

### Funcionalidades:
- **Criação de Tags**: Usuários podem criar tags com nomes únicos.
- **Associação de Tags**: Associar tags a diferentes conteúdos ou categorias.
- **Visualização de Nuvem de Tags**: Exibir uma nuvem de tags onde as mais usadas são destacadas.
- **Busca por Tags**: Permitir que usuários busquem conteúdos através de tags.

### Módulos:
1. **Modelo de Tag**: Define a estrutura de dados para as tags.
2. **Controlador de Tags**: Gerencia as requisições HTTP para criar, visualizar e associar tags.
3. **Visualização de Tags**: Cria a interface do usuário para interagir com as tags.
4. **Serviço de Busca**: Implementa a lógica para buscar conteúdos baseados em tags.

### Ferramentas e Práticas:
- **Trello**: Para gerenciamento de tarefas e planejamento do projeto.
- **GitHub**: Como repositório de código e para controle de versão.
- **Heroku**: Para hospedar e lançar a aplicação na web.

Aqui está um exemplo de como você pode definir o modelo `Tag` em Ruby on Rails:

```ruby
class Tag < ApplicationRecord
  has_many :taggings
  has_many :contents, through: :taggings

  validates :name, presence: true, uniqueness: true
end
```

E um exemplo de teste usando RSpec para este modelo:

```ruby
require 'rails_helper'

RSpec.describe Tag, type: :model do
  it 'is valid with a unique name' do
    tag = Tag.new(name: 'Ruby')
    expect(tag).to be_valid
  end

  it 'is invalid without a name' do
    tag = Tag.new(name: nil)
    expect(tag).not_to be_valid
  end

  it 'does not allow duplicate names' do
    Tag.create(name: 'Ruby')
    tag = Tag.new(name: 'Ruby')
    tag.valid?
    expect(tag.errors[:name]).to include('has already been taken')
  end
end
```

Esses são apenas exemplos iniciais para te ajudar a começar. Você precisará expandir e modularizar o código para incluir todas as funcionalidades e testes necessários. 

Vou explicar como você pode associar tags aos conteúdos em sua aplicação. Vamos considerar que você já tenha criado o modelo `Tag` e o modelo `Content` (ou qualquer outro nome que você tenha escolhido para representar os conteúdos).

1. **Associação Muitos-para-Muitos**:
   - Para associar tags aos conteúdos, usaremos uma relação **muitos-para-muitos**. Isso significa que uma tag pode estar associada a vários conteúdos e um conteúdo pode ter várias tags.
   - No seu modelo `Tag`, adicione a seguinte linha:

     ```ruby
     class Tag < ApplicationRecord
       has_many :taggings
       has_many :contents, through: :taggings
       # Restante do código...
     end
     ```

   - No seu modelo `Content`, adicione:

     ```ruby
     class Content < ApplicationRecord
       has_many :taggings
       has_many :tags, through: :taggings
       # Restante do código...
     end
     ```

2. **Modelo de Associação (Tagging)**:
   - Crie um novo modelo chamado `Tagging`. Esse modelo representará a associação entre tags e conteúdos.
   - Execute o seguinte comando para gerar o modelo:

     ```bash
     rails generate model Tagging tag:references content:references
     ```

   - Isso criará um arquivo de migração. Execute a migração com:

     ```bash
     rails db:migrate
     ```

3. **Lógica de Associação**:
   - Agora você pode associar tags aos conteúdos no seu controlador ou serviço.
   - Por exemplo, ao criar/editar um conteúdo, você pode permitir que o usuário selecione tags relacionadas.
   - No seu controlador `ContentsController`, você pode fazer algo assim:

     ```ruby
     def create
       @content = Content.new(content_params)
       @content.tag_ids = params[:tag_ids] # Supondo que você tenha um campo de seleção de tags no formulário
       # Restante do código...
     end
     ```

4. **Visualização de Tags**:
   - Na visualização do conteúdo, você pode exibir as tags associadas a ele.
   - Por exemplo, em uma página de detalhes do conteúdo:

     ```erb
     <h2><%= @content.title %></h2>
     <p><%= @content.body %></p>
     <ul>
       <% @content.tags.each do |tag| %>
         <li><%= tag.name %></li>
       <% end %>
     </ul>
     ```

5. **Testes**:
   - Certifique-se de escrever testes para verificar se a associação está funcionando corretamente.
   - Teste a criação de conteúdo com tags associadas e a visualização das tags.

Lembre-se de adaptar essas instruções ao seu projeto específico.
