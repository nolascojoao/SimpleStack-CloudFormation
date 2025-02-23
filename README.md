# AWS CloudFormation - Template para Lançamento de Instância EC2

## Introdução
O **AWS CloudFormation** é um serviço da AWS que permite modelar e provisionar recursos da nuvem de forma declarativa, usando arquivos no formato JSON ou YAML. Com ele, é possível criar, atualizar e excluir infraestrutura de maneira automatizada e segura.

Este documento explica detalhadamente o funcionamento do **template CloudFormation** abaixo, que cria uma instância EC2 dentro de uma **VPC específica**.

---

## Template CloudFormation (YAML)

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Template para lançamento de instância EC2 na AWS dentro de uma VPC específica.'

Parameters:
  InstanceType:
    Description: 'Tipo da instância EC2'
    Type: String
    Default: 't2.micro'
    AllowedValues:
      - 't2.micro'
      - 't3.micro'
      - 't3.small'

  KeyName:
    Description: 'Nome do par de chaves SSH'
    Type: AWS::EC2::KeyPair::KeyName

  VpcId:
    Description: 'ID da VPC onde a instância será lançada'
    Type: AWS::EC2::VPC::Id

  SubnetId:
    Description: 'ID da Subnet dentro da VPC'
    Type: AWS::EC2::Subnet::Id

Resources:
  TestEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      SubnetId: !Ref SubnetId
      SecurityGroupIds:
        - !Ref InstanceSecurityGroup
      Tags:
        - Key: Name
          Value: TestEC2Instance

  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: 'Permitir acesso SSH'
      VpcId: !Ref VpcId
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-05b10e08d247fb927
```

---

## Explicação do Funcionamento

### **1. AWSTemplateFormatVersion**
Define a versão do CloudFormation utilizada (`2010-09-09`).

### **2. Description**
Fornece uma breve descrição do template.

### **3. Parameters (Parâmetros)**
- **InstanceType**: Permite ao usuário escolher o tipo da instância EC2 (t2.micro, t3.micro, t3.small).
- **KeyName**: Nome do par de chaves SSH para acessar a instância.
- **VpcId**: ID da VPC onde a instância será criada.
- **SubnetId**: Subnet dentro da VPC onde a instância será lançada.

### **4. Resources (Recursos)**

#### **4.1. Instância EC2**
- Criada com o tipo definido em `InstanceType`.
- Associada a um **par de chaves SSH**.
- Obtém a **AMI** a partir do mapeamento de região (`Mappings`).
- Conectada à Subnet especificada.
- Protegida pelo **Security Group** criado.
- Recebe a tag `TestEC2Instance`.

#### **4.2. Security Group**
- Criado para permitir **acesso SSH (porta 22)** de qualquer IP (`0.0.0.0/0`).
- Associado à VPC informada pelo usuário.

### **5. Mappings (Mapeamentos)**
- Define uma **AMI específica para a região `us-east-1`**.
- O `ImageId` da instância EC2 é obtido dinamicamente com `!FindInMap [RegionMap, !Ref "AWS::Region", AMI]`.

---

## **Resumo do Funcionamento**
1. **O usuário fornece os parâmetros necessários** ao criar a pilha (**stack**).
2. **O CloudFormation cria os recursos na AWS**:
   - Um **Security Group** permitindo acesso SSH.
   - Uma **instância EC2** dentro da VPC/Subnet definidas.
3. **A instância recebe a AMI correspondente à região**, conforme o `Mappings`.

Esse template é uma ótima base para lançamentos automatizados de instâncias EC2 na AWS. 🚀


---

![stack-config](https://github.com/user-attachments/assets/205b934b-e0ee-4652-99fc-fa76b7893006)
