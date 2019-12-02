## [gpg](https://tech.eduardoelias.com/2015/tutorial-gpg.html)

**GPG** ou **Gnu Private Guard** é uma ferramenta pra comunicação segura. Nesse post vou exemplificar algumas maneiras de usar tentando descrever alguns casos de uso. Se quiser uma explicação mais detalhada do que é, você pode encontrar [wikipedia](https://pt.wikipedia.org/wiki/GNU_Privacy_Guard) ou no [site oficial](https://www.gnupg.org/).

Também fiz um vídeo que está no final do artigo para mostrar as coisas que descrevo aqui em ação.

Os dois tipos de critpografia que vou mostrar são as *Simétrica* e *Assimétrica*. São os tipo mais úteis que conheço e cobrem 99.9% dos casos.

Todos os exemplos mostrados valem para qualquer tipo de *conteúdo*. Uso a palavra *conteúdo* porque é valido não só para um texto, um documento, slides, fotos, vídeos ou arquivos binários. Qualquer *conteúdo* que seja preciso proteger e que só consiga ter acesso a pessoa que possuir a chave para descriptografar.

## Pré-requisitos

Instale o GPG no seu computador. [Nesse link](https://www.gnupg.org/download/index.html) você pode encontrar todos os binários para praticamente todas as plataformas. Até para [Android](https://guardianproject.info/code/gnupg/) onde é possível usar os comandos usando [Android Terminal Emulator](https://play.google.com/store/apps/details?id=jackpal.androidterm).

O GPG possui **MUITAS** opções. Todas são acessíveis digitando --help:

```
local@host $ gpg --help
Syntax: gpg [options] [files]
Sign, check, encrypt or decrypt
Default operation depends on the input data

Commands:

 -s, --sign [file]             make a signature
     --clearsign [file]        make a clear text signature
 ...........
```

Nesse tutorial não vou cobrir todas as opções. Vou mostrar aquilo que acho que será útil.

> Para se aprofundar no assunto é só consultar [o manual](https://www.gnupg.org/gph/en/manual.html) que está mais completo (infelizmente não conheço uma fonte em português).

## Criptografia Simétrica

Eu gosto de pensar que é uma criptografia simples, direta. Em resumo funciona quando algum conteúdo é criptografado usando apenas uma chave(ou senha) onde só essa chave é necessária para descriptografar e ter acesso ao conteúdo.

Para criptografar um texto:

```
local@host $ gpg --symmetric meu_texto.txt
Enter passphrase:
```

Ou uma foto:

```
local@host $ gpg --symmetric minha_foto.jpg
Enter passphrase:
```

você vai ter que digitar a mesma senha duas vezes. *A dica de senhas muito fáceis se aplicam aqui.* Quanto mais complexa e maior, melhor. O gpg não vai se importar se você usar *123* como senha, mas não será muito complicado para alguém que tiver acesso ao arquivo conseguir descriptografar.

Depois de entrar a confirmação da senha você verá que apareceu um arquivo *meu_texto.txt.gpg* ou *minha_foto.jpg.gpg*. Esse arquivo com final *.gpg* é o seu conteúdo criptografado.

Note que o arquivo original ainda está intacto. Você pode apagar o arquivo original e a partir daí, apenas quem souber a chave(senha) definida, terá acesso ao arquivo.

Para descriptografar o arquivo:

```
local@host $ gpg --output meu_texto.txt --decrypt meu_texto.txt.gpg
Enter passphrase:
```

A opção --output diz qual será o nome do arquivo depois de descriptografado. Assim como quando foi criptografado, o arquivo *.gpg* não será apagado automaticamente.

### Casos de uso

#### 1) Computador compartilhado

Você possui algum conteúdo (texto/foto/vídeo) que não quer que ninguém mais veja, mas outras pessoas tem acesso ao mesmo computador e podem acessar aquele conteúdo sem sua permissão. Você pode criptografar o conteúdo e só você terá acesso.

Criptografe o contéudo que quer proteger. Apague a versão original e sempre que quiser acessar esse conteúdo você descriptografa e apaga logo depois que terminar. O seu arquivo criptografado sempre estará lá.

#### 2) Envio para uma amigo/amiga

Você quer enviar uma arquivo para alguém, mas sabe que essa pessoa compartilha o email ou você envia em um pendrive e sabe que outras pessoas podem olhar aquele pendrive. Você precisa apenas contar a senha para seu amigo/amiga e ele/ela terá acesso ao conteúdo.

Esse caso também se aplica para um grupo de amigos. Mas é claro que quanto mais pessoas souberem da senha, menos privado será o conteúdo já que o acesso ao arquivo e a senha são as únicas coisas que precisa para ver o contúedo.

## Criptografia Assimétrica

A criptografia assimétrica é o tipo de criptografia composta de duas chaves: *Chave privada* e *Chave pública*.

Ela funciona quando se criptografa um conteúdo com uma chave pública e uma vez criptografado é necessário ter a chave privada para descriptografar e conseguir acessar o conteúdo.

A chave na criptografia assimétrica é muito grande. Impossível de ser memorizada como no exemplo da chave simétrica. Por isso ela costuma ficar em um arquivo que é protegido por senha (usando a criptografia assimétrica).

A chave pública, como o nome já diz, é uma chave que não há problema outras pessoas verem. Na verdade é o que você quer, pois é com ela que as pessoas vão criptografar as coisas que só você terá acesso.

O processo para se usar a criptografia assimétrica é mais complexo. Antes de tudo é precisa gerar suas chaves (público e privada). Só existe uma chave privada para cada chave pública.

Para gerar a suas chaves:

```
local@host $ gpg --gen-key
gpg (GnuPG) 1.4.12; Copyright (C) 2012 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
```

Não quero ficar me alongando explicando esses tipos. O padrão *RSA e RSA* é suficiente.

> Não confundir com [NSA](https://pt.wikipedia.org/wiki/Agência_de_Segurança_Nacional) :)

Aqui você pode escolher o tamanho da chave. Quanto maior melhor ( máximo é 4096 ). Precisa ser a maior chave possível? Não. Mas nós queremos que seja. As outras opções são: 1024 e 2048.:

```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
```

Agora é hora de definir se suas chaves expiram. Mas como assim? Pois é. As chaves podem ter validade. A chave pública, por ser pública, fica publicada em servidores pelo mundo para que tenha um acesso mais facilitado pelo máximo de pessoas possível. Pode ser que não pareça tão interessante para pobres mortais, mas imagina um cara que faz parte de um projeto global. Ele não tem como ficar enviando a chave dele pra todo mundo, ou confirmando se tal chave é a chave pública dele. Ele simplesmente publica em um servidor de chaves públicas e assim todos tem acesso.

E é ai que entra a utilidade da validade da chave. A pessoa não tem como avisar todo mundo qual sua chave, ele também não tem como avisar quando ele parar de usar a chave.

Alguém pode ter vários motivos para querer parar de usar uma chave:

- Pode ter sido comprometida: ao contrário da chave simétrica, não se compartilha chave privada. Cada um cria sua chave, distribui a chave pública e guarda bem guardado a chave privada (criptografada por uma boa senha).
- Pode ser uma chave temporária
- A pessoa pode assumir que ficar muito tempo com a mesma chave não é seguro...
- Qualquer outro motivo

Mas mesmo depois de dito tudo isso se quiser que a chave não expire:

```
Please specify how long the key should be valid.
         0 = key does not expire (chave não expira NUNCA)
      <n>  = key expires in n days (chave expira em n dias)
      <n>w = key expires in n weeks (chave expira em n semanas)
      <n>m = key expires in n months (chave expira em n meses)
      <n>y = key expires in n years (chaves expira em n anos)
Key is valid for? (0)
Key does not expire at all
Is this correct? (y/N) y
```

Essa opção pode ser alterada no futuro se por acaso mudar de ideia.

Hora de definir alguns detalhes da sua chave. *Nome real*, *email*, *algum comentário*. Esses dados vão aparecer quando sua chave for enviada para um servidor. Quem estiver procurando por sua chave vai poder usar algum desses dados na busca. Logo depois de confirmar essas informações será necessário definir uma senha para criptografar sua chave primária. Sem essa senha, mesmo tendo a chave primária a pessoa não conseguirá descriptograr aquilo que foi criptografado com sua chave pública.:

```
You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: Nome do usuario ligado a essa chave
Email address: meu@email.com
Comment:
You selected this USER-ID:
    "Nome do usuario ligado a essa chave <meu@email.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.
```

Essa é outra diferença da criptografia simétrica (se tiver o arquivo e a senha é possível acessar o conteúdo), com a criptografia assimétrica a chave privada está criptografada, é necessário ter o arquivo criptografado, a chave privada e a senha dessa chave privada para acessar o conteúdo. Precisar de mais partes torna esse método mais seguro.:

```
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
.+++++
.+++++
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
.+++++
..........+++++
gpg: key 0x21F1FFE8F896A96F marked as ultimately trusted
public and secret key created and signed.
```

Aqui tem outra coisa que pode parecer estranho. Sua chave são geradas usando [entropia](https://pt.wikipedia.org/wiki/Entropia). É só ir mexendo no mouse, apetar algumas teclas e depois de algum tempo suas chaves serão geradas.:

```
gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   4096R/0x21F1FFE8F896A96F 2015-03-02
      Key fingerprint = C59A 91D2 409E 086E 4AC6  C65A 21F1 FFE8 F896 A96F
uid                 [ultimate] Nome do usuario ligado a essa chave <meu@email.com>
sub   4096R/0xC4D1941CA83B2D16 2015-03-02
```

Esses são os dados da chave que eu gerei como teste. Vamos dar uma olhada na sua chave pública:

```
local@host $  gpg --export --armor

-----BEGIN PGP PUBLIC KEY BLOCK-----

mQINBFO/j4oBEADREGSWCY548aSRy4p4g7CASuZbc9wovGIz6Lg/OCEjKYHFYkjZ
ds5gCfga8Ycq1A+TMNYcyClsWpiEGFiNA3legqzd3PxMswlHzQVywwtoahRnAb1F
G4B0RCW6ZU1PU7x0wAhBAqWpXiAaOjQYYMbrCsVljphBwUD+0sqHqfE4H5Gx9WEr
wCQgIhsm4UnUF200h82+XUBm0fQ1Q1bAHUKVAluFtNuQo/F370/y76HBeu/sxV1A
1Tws370F6WneZiFu9d4MggMyKO2VPBjRPliaDR9mxMw8zCugrU6LznjfW58TgkwL
5PnBp49MsBnfZS9ZXo6aK2qIl/rBO6wITsbeBiKbyLYIXyhTUbNC1TtIjLGKDtds
zuMJD39up6hcNWHuHv/mHvrNmCGmWgukts9lWCiXlhwWMjwLvFGs+kkwhMzdMHlY
XlZVt3GpTjFXekJ9F/EY8ND9p4uRtO5yxFEgGWwdG14ww2Q3wh82+hlzVSU7CCSV

...... MONTE DE COISA ESCRITA.....................

vY5+E0l4ljC+vTi6QoD5zRRv0dANzB3WGor87nRTr9ib4+UYrKorHNdjP91R+hhY
E9VLxLX8VDX/QImmgxuRu4gFt4wCFQU77XdmPMf36BhNE3CwzlygSsdQSHJXwklC
RI66DSbl8qJGgW2OUtgFZ0/gBWFQRLsqXpK4RXyWUe9iKw7a5GmRi1KJwAUJSIEu
MO+HhK+rG4kz7ivgkDw5fnMSsXPv3ORpG/2XsBsHW4ZTqnTBwT0BfTqtip3FCGv9
iBs=
=7Ik4
-----END PGP PUBLIC KEY BLOCK-----
```

Essa é sua chave pública. Fácil de decorar né? :) Pois é, você passa isso para alguém de várias formas:

- Imprime e entrega no papel mesmo

ou

```
local@host $ gpg --export --armor --output minha_chave_publica.pub
```

- poe em um servidor de chaves (vamos ver mais adiante)
- Manda por email
- poe em um pendrive
- etc...

### Casos de uso

#### 1) Usando chaves públicas recebidas

E agora que você recebeu a chave pública de outra pessoa:

```
local@host $ gpg --import arquivo_com_chave.pub
```

Isso vai fazer com que chave publica seja adicionada na sua lista de chaves:

```
local@host $ gpg --list-keys

pub   2048R/4257419B 2015-03-02 [expires: 2015-03-04]
     Key fingerprint = B51A 8C41 2591 61C5 A354  3653 8F34 EBE5 4257 419B
uid       [ultimate] meu nome <e@mail.com>
sub   2048R/637863ED 2015-03-02 [expires: 2015-03-04]
```

Você deve estar vendo sua chave e todas as chaves públicas importadas. Sua chave deve ser a última da lista e você vai identifica-la pela linha **uid**.

O identificador da sua chave está na primeira linha. Você também pode usar esse identificador para se referir a chave nos comandos como por exemplo editar a chave.:

```
pub 2048R/4257419B 2015-03-02 [expires: 2018-03-04]
```

- **pub**: Indica que essa chave é a pública
- **2048R**: é o tamanho da chave utilizado (se escolheu 4098 irá aparecer 4098R)
- **4257419B**: é sua chave, na verdade uma redução e pode ser usado assim como seu email quando for criptografar (gpg -e -r 4257419B)
- **2015-03-02**: data da criação da chave
- **[expires: 2018-03-04]** quando a chave irá expirar. Nesse caso em 2 dias. Se não aparecer nada, é que a chave não expira nunca.

#### 2) Criptografar usando uma chave pública

```
local@host $ gpg --encrypt --recipient email@doamigo.com arquivo_secreto.txt
```

ou usando os atalhos:

```
local@host $ gpg -e -r email@doamigo.com arquivo_secreto.txt
```

ou mesmo usando a chave:

```
local@host $ gpg -e -r 4257419B arquivo_secreto.txt
```

Nenhuma senha foi pedida porque não precisa de senha. Foi usada a chave pública da pessoa que vai receber o conteúdo (que pode ser você se apenas está querendo proteger o conteúdo) e uma vez que o arquivo original for apagado, só a pessoa que tem a chave privada relacionada aquela chave pública pode ter acesso ao conteúdo.

#### 3) Cripografaram conteúdo usando minha chave pública

Nesse caso para ler/ver o conteúdo você precisa descriptografar:

```
local@host $ gpg --decrypt arquivo_secreto.jpg.gpg --output arquivo_secreto.jpg
```

ou usando os atalhos:

```
local@host $ gpg -d arquivo_secreto.jpg.gpg -o arquivo_secreto.jpg
```

Aqui será necessário fornecer a senha. A senha será usada para descriptografar sua chave privada e então essa chave será usada para descriptografar o conteúdo.

#### 4) Assinar mensagens

Uma coisa que só é possível com as chaves assimétricas é a possibilidade de assinar conteúdo.

Assinar um conteúdo é afirmar que o conteúdo não foi alterado durante o envio. É uma maneira das pessoas que virem aquele conteúdo saberem que é não foi alterado por ninguém depois da sua assinatura. Você pode criptografar e ao mesmo tempo assinar um contéudo:

```
local@host $ gpg -e --sign -r amigo@email.com arquivo_secreto.txt
```

mas nesse caso só o *amigo* vai poder ler essa mensagem e vai saber que foi você quem criptografou, já que o arquivo está assinado.

Mas imagina que você faça uma postagem em um fórum ou envie um email para um grupo de discussão. Você *não* quer criptografar a mensagem porque você quer que todos leiam, mas você também quer que todos saibam que foi você quem enviou. Nesse caso você assina sem criptografar.:

```
local@host $ gpg --sign mensagem_para_forum.txt
```

usando o atalho:

```
local@host $ gpg -s mensagem_para_forum.txt
```

Um novo arquivo apareceu: *mensagem_para_forum.txt.asc* ele contém a sua mensagem mais a assinatura.:

```
local@host $ gpg --verify mensage_para_forum.txt.asc

gpg: Signature made Mon 02 Mar 2015 04:15:22 AM UTC
gpg:                using RSA key 0x21F1FFE8F896A96F
gpg: Good signature from "Nome do usuario ligado a essa chave <meu@email.com>" [ultimate]
Primary key fingerprint: C59A 91D2 409E 086E 4AC6  C65A 21F1 FFE8 F896 A96F
```

Se tentar mudar, qualquer coisa na mensagem (exceto espaços no final das linhas) que está junto com a assinatura, e tentar verificar a mensagem:

```
local@host $ gpg --verify mensage_para_forum.txt.asc

gpg: Signature made Mon 02 Mar 2015 04:15:22 AM UTC
gpg:                using RSA key 0x21F1FFE8F896A96F
gpg: BAD signature from "Nome do usuario ligado a essa chave <meu@email.com>" [ultimate]
```

Ou seja, você sabe que algo foi alterado no conteúdo entre a assinatura e o seu recebimento.

#### 5) Assinar chaves

Assinar chaves também é importante. Para criptografar uma mensagem e enviar para alguém você precisa da chave pública. Mas como saber se a chave pública que você encontrou é da pessoa que diz ser? Resumindo é um pouco de bom senso.

Mas se a pessoa for realmente paranóica ela vai esperar que as chaves públicas que ela vai usar sejam chaves assinadas por pessoas que ela conhece e confia.

Se você não é tão paranoico assim, simplesmente baixe a chave que está no site e seja feliz! :)

Como já vimos uma chave pública é uma string gigante, e como qualquer mensagem, pode ser assinada do mesmo jeito.

#### 6) Publicando sua chave pública

A última coisa que quero falar é como publicar sua chave pública para que outras pessoas tenham acesso.

A gente já viu como gerar sua chave:

```
local@host $ gpg --export --armor --output minha_chave_publica.pub
```

Você pode publicar no seu site esse arquivo ou enviar para alguém. Ou você pode publicar em servidores públicos de chaves:

```
local@host $ gpg --send-keys 4257419B
gpg: sending key 4257419B to hkps server  hkp://keys.gnupg.net
```

Quando sua chave estiver publicada, será possível para alguém achar sua chave usando o comando a opção --search-keys para te enviar algo cryptografado ou para confirmar sua assinatura:

```
local@host $ gpg --search-keys meu@email.com
gpg: searching for "meu@email.com" from kkps server....
```

Achando a chave procurada é só adicionar na sua lista de chaves.

> Note que cada lista é local. Se estiver usando sua chave em outro computador você terá que adicionar todas as chaves novamente a sua lista.

## Finalizando

Mais alguns comandos que gostaria de chamar atenção:

- exportar sua chave privada para que seja usada em outro lugar (é importante fazer back dessa chave)

```
local@host $ gpg --output minha_chave_privada --export-secret-key 4257419B
```

Esse arquivo terá sua chave privada criptografada. Para fazer backup e/ou importar em outra máquina:

```
local@host $ gpg --import minha_chave_privada
```

Se chegou até aqui parabéns! Quando comecei esse artigo não pensei que ficaria tão extenso.

Se viu algum erro e gostaria de enviar a correção via github, [fique a vontade](https://github.com/camponez/tech_blog)

https://youtu.be/r8k-jJ-EHP8
