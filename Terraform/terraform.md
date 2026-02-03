# 🧱 Blocos de Estrutura e Configuração

* **1. terraform:** Define as configurações do próprio Terraform, como a versão mínima exigida e onde o estado (`state`) será armazenado (ex: local ou no S3 da AWS).
* **2. provider:** É o "tradutor". Ele diz ao Terraform com qual API ele vai falar (AWS, Google Cloud, Azure, Kubernetes). Sem ele, o Terraform não sabe como criar recursos.
* **5. module:** Um pacote de recursos reutilizáveis. Em vez de escrever 50 linhas toda vez que criar um servidor, você cria um módulo e o chama como uma função.

# 🏗️ Blocos de Dados e Recursos

* **3. resource:** O coração de tudo. É aqui que você descreve o que quer criar (uma instância EC2, um banco de dados, uma rede).
* **4. data:** Serve para **consultar** informações de algo que já existe fora do seu código atual. Exemplo: buscar o ID de uma imagem (AMI) oficial do Ubuntu na AWS.

# 📥 Entradas, Saídas e Lógica

* **6. variable:** Define valores que podem ser alterados sem mexer no código principal (inputs). Como os argumentos de uma função.
* **7. output:** Exibe informações importantes no terminal após a execução, como o IP público de um servidor criado.
* **8. locals:** Variáveis internas para evitar repetição. Servem para guardar cálculos ou nomes complexos que você usa várias vezes dentro do mesmo módulo.

# 🔄 Gerenciamento de Ciclo de Vida e Refatoração

* **9. import:** Traz recursos que foram criados manualmente (via painel/clique) para dentro do controle do Terraform.
* **10. moved:** Usado para renomear recursos ou movê-los para dentro de módulos sem que o Terraform tente destruí-los e criá-los do zero.
* **11. removed:** Permite remover um recurso do estado do Terraform (parar de gerenciá-lo) sem necessariamente deletar o recurso real na nuvem.
* **12. check:** Introduzido em versões recentes, serve para validar se a infraestrutura está saudável após a criação (ex: verificar se uma URL está respondendo 200 OK).

---

### Quando usar cada um? (Resumo Rápido)

| Se você quer... | Use o bloco... |
| --- | --- |
| Criar algo novo | `resource` |
| Consultar algo existente | `data` |
| Organizar e reutilizar código | `module` |
| Parametrizar o código | `variable` |
| Renomear algo sem deletar | `moved` |

