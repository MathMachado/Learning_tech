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

# Exemplo

Para que este código funcione, ele está dividido logicamente. O Terraform lê todos os arquivos `.tf` na mesma pasta, mas o padrão de mercado é organizar assim:

## 1. `variables.tf` (As Entradas)

Aqui definimos o que pode mudar sem precisarmos editar o código principal.

```hcl
variable "instance_name" {
  description = "Nome da tag para a instância"
  type        = string
  default     = "Servidor-Projetos-AI"
}

variable "instance_type" {
  description = "Tipo da instância (tamanho do hardware)"
  type        = string
  default     = "t3.micro"
}

```

## 2. `main.tf` (A Infraestrutura)

Aqui é onde a mágica acontece. Usamos o `provider` para conectar, o `data` para buscar a imagem correta e o `resource` para criar o servidor.

```hcl
# 1. Configuração do Terraform e Provider
terraform {
  required_version = ">= 1.0.0"
}

provider "aws" {
  region = "us-east-1"
}

# 2. Bloco Data: Busca a AMI mais recente do Ubuntu
data "aws_ami" "ubuntu" {
  most_recent = true
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
  owners = ["099720109477"] # Canonical
}

# 3. Bloco Locals: Organiza nomes repetitivos
locals {
  common_tags = {
    Project   = "DataScience-Automation"
    ManagedBy = "Terraform"
  }
}

# 4. Bloco Resource: Cria o recurso real
resource "aws_instance" "web_server" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type # Usando a variável

  tags = merge(locals.common_tags, {
    Name = var.instance_name
  })
}

```

## 3. `outputs.tf` (As Saídas)

Útil para você saber o endereço do servidor assim que ele terminar de ser criado.

```hcl
output "instance_public_ip" {
  description = "IP público da instância criada"
  value       = aws_instance.web_server.public_ip
}

```

---

## Por que essa estrutura é boa?

1. **Flexibilidade:** Se você quiser mudar o nome do servidor, você só altera o valor da `variable` (ou passa via linha de comando), sem tocar no recurso.
2. **Inteligência:** O bloco `data` garante que você sempre pegue a versão mais nova do Ubuntu disponível na AWS, evitando que seu código fique "viciado" em um ID de imagem antigo.
3. **Organização:** Usar `locals` permite que você aplique as mesmas tags (como nome do projeto) em dezenas de recursos de uma vez só.

