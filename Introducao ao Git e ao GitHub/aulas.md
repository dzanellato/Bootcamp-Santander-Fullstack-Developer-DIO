## Git

Sistema de versionamento de código distribuído.

## Github

Um repositório online para armazenamento de código.

## Benefícios de utilizar Git e Github

- Controle de versionamento de código;
- Armazenamento em nuvem;
- Trabalho em equipe;
- Melhoramento de código (devido ao trabalho em equipe / colabrações); 
- Reconhecimento;

## SHA1

Secure Hash Algorithm, é um conjunto de funções hash critográficas projetadas pela NSA. 

Para um arquivo de texto, a encriptação gera um conjunto de caracteres **identificador** com 40 dígitos, i.e., uma representação "curta" e única do texto.

Por exemplo,

```bash
echo "ola mundo" | openssl sha1
(stdin)= d9802fa01c4c1dfc4ddaf61f766d8d56ad8a8699
```

A sequência (*hash*) `d9802fa01c4c1dfc4ddaf61f766d8d56ad8a8699` é um identificador único para o texto `ola mundo`. Uma pequena alteração do texto leva a uma sequência completamente diferente:

```bash
echo "olá mundo" | openssl sha1
(stdin)= 3edcbcfed580bf9dbc51951c49497639403bca73
```

É através desta representação que o Git verifica se alguma modificação ocorreu em um código fonte.


Obs.: o *hash* é o identificador dos commits fornecido pelo Git.

## Blobs, Trees, Commits

São objetos internos utilizados pelo Git.

### Blob

É um objeto que contém metadados e o conteúdo do arquivo que estamos versionando. Mais precisamente, a estrutura de um Blob é a seguinte:

    blob
    (tamanho do arquivo)
    \0
    (conteúdo do arquivo que estamos versionando)

Por exemplo, o *hash* abaixo gerado pelo Git

```bash
echo "ola mundo" | git hash-object --stdin
6d6648c00eb7a59d141d1f85a6678ba1a5995547
```

é idêntico ao gerado a seguir

```bash
echo "blob 10\0ola mundo" | openssl sha1   
(stdin)= 6d6648c00eb7a59d141d1f85a6678ba1a5995547
```

A "blob" object is nothing but a chunk of binary data. It doesn't refer to anything else or have attributes of any kind, not even a file name ([Git Community Book][1]).

![alt text](./images/object-blob.png "Blob")

You can use git show to examine the contents of any blob. Assuming we have the SHA for a blob, we can examine its contents like this:

```{bash}
git show 6ff87c4664

 Note that the only valid version of the GPL as far as this project
 is concerned is _this_ particular version of the license (ie v2, not
 v2.2 or v3.x or whatever), unless explicitly otherwise stated.
...
```

A "blob" object is nothing but a chunk of binary data. It doesn't refer to anything else or have attributes of any kind, not even a file name.

Since the blob is entirely defined by its data, if two files in a directory tree (or in multiple different versions of the repository) have the same contents, they will share the same blob object. The object is totally independent of its location in the directory tree, and renaming a file does not change the object that file is associated with.


### Trees 

A tree is a simple object that has a bunch of pointers to blobs and other trees - it generally represents the contents of a directory or subdirectory ([Git Community Book][1]). 

![alt text](./images/object-tree.png  "Tree")

An object referenced by a tree may be blob, representing the contents of a file, or another tree, representing the contents of a subdirectory. Since trees and blobs, like all other objects, are named by the SHA1 hash of their contents, two trees have the same SHA1 name if and only if their contents (including, recursively, the contents of all subdirectories) are identical. This allows git to quickly determine the differences between two related tree objects, since it can ignore any entries with identical object names.


### Commit

The "commit" object links a physical state of a tree with a description of how we got there and why ([Git Community Book][1]).

![alt text](./images/object-commit.png "Tree")


As you can see, a commit is defined by:

- a tree: The SHA1 name of a tree object (as defined below), representing the contents of a directory at a certain point in time.
- parent(s): The SHA1 name of some number of commits which represent the immediately previous step(s) in the history of the project. The example above has one parent; merge commits may have more than one. A commit with no parents is called a "root" commit, and represents the initial revision of a project. Each project must have at least one root. A project can also have multiple roots, though that isn't common (or necessarily a good idea).
- an author: The name of the person responsible for this change, together with its date.
- a committer: The name of the person who actually created the commit, with the date it was done. This may be different from the author; for example, if the author wrote a patch and emailed it to another person who used the patch to create the commit.
- a comment describing this commit.

Note that a commit does not itself contain any information about what actually changed; all changes are calculated by comparing the contents of the tree referred to by this commit with the trees associated with its parents. In particular, git does not attempt to record file renames explicitly, though it can identify cases where the existence of the same file data at changing paths suggests a rename.


### The Object Model

So, now that we've looked at the 3 main object types (blob, tree and commit), let's take a quick look at how they all fit together ([Git Community Book][1]).

If we had a simple project with the following directory structure:

```
|-- README
`-- lib
    |-- inc
    |   `-- tricks.rb
    `-- mylib.rb
```

And we committed this to a Git repository, it would be represented like this:

![alt text](./images/objects-example.png "Tree")

You can see that we have created a tree object for each directory (including the root) and a blob object for each file. Then we have a commit object to point to the root, so we can track what our project looked like when it was committed.

[1]: http://shafiul.github.io/gitbook/1_the_git_object_model.html


## Git é um sistema distribuído seguro

Possuí diversas cópias idênticas de si mesmo em locais diferentes.


Esse modelo de objetos garante que o status de um projeto em um determinado commit seja o mesma para todos os mantenedores do projeto. Desta forma, caso ocorra algum problema com o repositório que hospeda o projeto, este último poderia ser recuperado  partir da cópia de algum mantenedor.


## Chave SSH e Token

A autenticação por senha no Github tornou-se obsoleto a partir de agosto 2021.


### Autenticação por chave SSH

*Grosso modo*, é uma forma de estabelecer uma conexão segura e encriptada entre duas máquinas. Este tipo de conexão será utilizada para nos conectarmos ao servidor do Github. 

Para isso, precisaremos gerar um par de chaves SSH, uma pública e outra privada, e informarmos a chave pública ao Github: 

```
Settings > SSH e GPG Keys
```

#### Para gerar as chaves

```{bash}
ssh-keygen -t ed25519 -C "email_cadastrado_github"
```

e definir uma passphrase.

O conteúdo da chave pública (.pub) localizada em `~/.ssh` deve ser colocado no Github.

Após isso, executar: 
- `eval $(ssh-agent -s)` que iniciará um processo no background
- `ssh-add path_da_chave_privada`
- entrar com a passphrase

## Autenticação por token de acesso pessoal

Funciona como logine senha para realizar `git clone` com HTTPS.


Gerar o token no github em 

    Settings > Developer settings > Personal acess tokens

e salvar o token gerado num local seguro.

Após o `git clone`, abrirá uma janela do Github para realizarmos um Sign in com o token.


## Primeiros comandos

### Criando um repositório local

Na pasta local do repositório:

```
git init
```

Este comando cria a pasta (oculta) `.git`. Dentro dela existe a pasta `objects` que contem os objetos blob, tree e commit discutidos anteriormente.

#### Configurando email e user 

Estas informações são usadas durante os commits;

```
git congif --global user.email  "email_usado_do_github"
git congif --global user.name  "usuario_do_github"
```

**Obs**.: embora as configurações acimas foram definidas globalmente (para todos os repositórios), as mesmas poderiam ter sido definidas localmente.


#### Staging as mudanças

Staging de todas as modificações:

```
git add .
```

#### Criando um commit

```
git commit -m "descricao do commit"
```

#### Verificando o status dos arquivos do repositório local

```
git status
```

Informa quais arquivos estão nos seguintes status:

- **untracked**: arquivos recentes ainda não rastreados pelo Git
- **modified**: arquivos rastreados pelo Git que sofreram modificações desde o último commit
- **staged**: arquivos aguardando o commit (já passaram pelo git add "nome_do_arquivo")
- etc.

#### Verificar todas as configurações do repositório local

```
git config --list
```

Caso precise apagar o user ou email configurado:

```
git config --global --unset user.email
git config --global --unset user.name
```

#### Apontar o repositório local ao remoto

```
git remote add origin link_https_do_repositorio_no_github
```

**Obs**.: `origin` é apenas um alias do https do repositório remoto e pode ser definido com outor nome. 

#### Listar os repositórios remotos

```
git remote -v
```


#### Empurrando o repositório local para o remoto

```
git push origin master
```
