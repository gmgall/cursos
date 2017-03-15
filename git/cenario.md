# Cenários do treinamento de git

## Primeiros *commits*

* Vamos criar os arquivos iniciais do projeto.

* Crie um arquivo chamado `occ_counter.sh` com o seguinte conteúdo:

```
#!/bin/bash
#
# occ_counter.sh - Conta as ocorrências de espécies num CSV no formato:
#
# "2","Aburria jacutinga",-45.4167,-23.8
# "3","Aburria jacutinga",-47.98,-24.1756
# "18","Aburria jacutinga",-54.44626,-25.68337

sed -n '1,$p' "$1" |
	cut -f4 -d\" |
	sort |
	uniq -c |
	sort -n -r
```

* Crie um arquivo chamado `README.md` com o seguinte conteúdo:

<pre>
# Contador de ocorrências de espécies

O script `occ_counter.sh` recebe um arquivo com ocorrências de espécies no
formato

```
"109","Aburria jacutinga",-48.4059,-24.1647
"118","Aburria jacutinga",-48.4181,-24.3013
"1823","Agelasticus cyanopus",-56.662,-16.3834
"1845","Agelasticus cyanopus",-56.9292,-17.1174
"2145","Agelasticus cyanopus",-56.9431,-17.1025
"2319","Alopochelidon fucata",-46.775,-20.3338
"2415","Alopochelidon fucata",-47.8945,-15.9383
"3042","Amaurospiza moesta",-57.2309,-19.2545
```

e informa uma lista com o número de ocorrências por espécie na saída padrão.

Exemplo de saída:

```
     63 Agelasticus cyanopus
     49 Alopochelidon fucata
     20 Aburria jacutinga
     15 Amaurospiza moesta
```
</pre>

* Crie um arquivo `.gitignore` para orientar o git a não versionar os CSVs.

* Faça o *commit* inicial.

* Adicione uma licença ao projeto num arquivo chamado LICENSE. Após ampla discussão com o departamento jurídico decidimos que a licença adequada é a Beer License:

```
"A LICENÇA BEER-WARE ou A LICENÇA DA CERVEJA" (Revisão 43 em Portugués Brasil):
<gmgall@gmail.com> escreveu este arquivo. Enquanto esta nota estiver na coisa você poderá utilizá-la
como quiser. Caso nos encontremos algum dia e você me reconheça e ache que esta coisa tem algum
valor, você poderá me pagar uma cerveja em retribuição (ou mais de uma).
```

---------

* Você percebe um *bug*: o programa não avisa se o arquivo de ocorrências não puder ser lido. Vamos corrigi-lo num *branch* separado.

## Merge fast-foward

* Crie um *branch* chamado `bugfix`.

* Adiciona as seguintes linhas em occ_counter.sh:

```
# Verifica se o arquivo pode ser lido
[ ! -e "$1" ] && { echo "Não consegui ler $1"; exit 1; }
```

* Teste e comprove que tudo ficou OK.

* Faça um *commit* com a mensagem `Faz occ_counter.sh avisar se o arquivo de ocorrências não puder ser lido`.

* Mude para o *branch* `master`.

* Faça o *merge* de `master` com `bugfix`. Repare no aviso de *merge* FF.

---------

* Você quer adicionar uma contagem do total de espécies no final do relatório.

## 3-way merge (tranquilo)

* Crie um *branch* chamado `feature_num_spp`.

* Troque para o *branch* `feature_num_spp`.

* E muda o trecho de `occ_counter.sh` de

```
# Mostra a lista com a contagem das ocorrências
sed -n '1,$p' "$1" |
        cut -f4 -d\" |
        sort |
        uniq -c |
        sort -n -r
```

para

```
# Gera a lista com a contagem das ocorrências
LISTA="$(sed -n '1,$p' "$1" |
        cut -f4 -d\" |
        sort |
        uniq -c |
        sort -n -r)"
```

e adicione

```
# Mostra a lista
echo "$LISTA"

# Mostra o total de espécies
echo
echo Total de espécies $(wc -l <<< "$LISTA")
```

* No README você avisa da nova funcionalidade

```
      1 Carduelis yarrellii
      1 Calyptura cristata
      1 Attila spadiceus
      1 Atticora melanoleuca

Total de espécies 474
```

* Beleza, ficou legal.

* Vamos fazer o *commit* dessas alterações.

* Antes que a gente possa fazer o *merge*...

:telephone_receiver::telephone_receiver::telephone_receiver: Chefe liga e pede para criar um `CONTRIB`, imediatamente! :telephone_receiver::telephone_receiver::telephone_receiver:

* Faça `checkout` para o *branch* `master`.

* Crie o `CONTRIB`.

* Faça o *commit* no `master`.

* Com o chefe satisfeito, de volta ao feature_num_spp **para testar tudo**.

* OK, tudo certo.

* Voltemos ao `master`...

* ...e façamos o *merge*.

**Observe que não foi FF. Um commit de merge foi criado!**

* Para que diabos você está me mostrando essa diferença? Eu só quero versionar meu projeto!

---------

## 3 way merge (conflito)

* Você acha o `sed` complicado de ler e quer trocá-lo por `cat`.

* Crie um *branch* chamado `troca_sed`.

* Edite o arquivo `occ_counter.sh` e troque o `sed` por `cat`.

* Faça o *commit*.

* Mudança feita, voltemos ao *branch* `master` para fazer o *merge*.

* Teste o código e :warning::warning: perceba o *bug* do cabeçalho. O cabeçalho é contado como uma espécie! :warning::warning:

* Corrija o *bug* (só mudar o `sed` para começar a ler da 2ª linha).

* Faça *commit* da correção.

* Ok, correções feitas. Voltemos ao nosso *merge* do branch `troca_sed`.

:scream: CONFLITO!
```
$ git merge troca_sed 
Mesclagem automática de occ_counter.sh
CONFLITO (conteúdo): conflito de mesclagem em occ_counter.sh
Automatic merge failed; fix conflicts and then commit the result.
```

* Para resolver os conflitos:

1. marque-os como resolvidos com `git add`
1. faça `git commit`

* Apague o *branch* `troca_sed`.

---------

## Colocando nosso repositório no github


* Crie um repositório no github

* Adicione o remoto

* Use o comando `push`:

```
git push -u origin master
```