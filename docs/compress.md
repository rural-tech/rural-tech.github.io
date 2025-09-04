---
title: Compressão
nav_order: 2
---

# Formato `.laz`

Tanto o formato `.las` como o formato `.laz` são formatos de arquivos de pontos
3D binários e de código aberto. A diferença entre eles é que o segundo é a
versão compactada do primeiro, possuindo um tamanho em torno de 7~20% do
original [^laz_git]. O formato `.laz` utiliza a versão mais recente do formato
`.las` (1.4) [^laz_specification] e é considerado pela OGC (_Open Geospatial
Consortium_) como o padrão industrial para dados comprimidos de LiDAR
[^laz_standard].

O formato `.las` possui uma _header_, com tamanho de 375 bytes na versão mais
recente (1.4), contendo informações de metadados, como por exemplo:
identificação, versão, formato de dados, fatores de escala e _offsets_ e
_bounding box_ [^las_specification]. Também é possível o armazenamento de
outras informações, como o sistemas de coordenadas, em campos de VLR (_Variable
Length Record_).

As informações de posição são armazenadas como inteiros sem sinal de 32 bits,
devem ser aplicados os valores de escala e _offsets_ para obter o valor real
[^las_specification], na forma:

$$
\begin{aligned}
  X_{\text{coordinate}} &= (X_{\text{record}} \times X_{\text{scale}}) + X_{\text{offset}} \\
  Y_{\text{coordinate}} &= (Y_{\text{record}} \times Y_{\text{scale}}) + Y_{\text{offset}} \\
  Z_{\text{coordinate}} &= (Z_{\text{record}} \times Z_{\text{scale}}) + Z_{\text{offset}}
\end{aligned}
$$

Para a compressão e decompressão de arquivos no formato `.laz`, o software
`Lastools Integration` pode ser utilizado:

{:style="text-align:center;"}
![Lastools Integration compressão de LAS](./assets/images/lastools-convert-laz.png)

[^laz_git]: [LASzip GitHub](https://github.com/LASzip/LASzip)

[^laz_specification]:
    [LAZ Specification](https://downloads.rapidlasso.de/doc/LAZ_Specification_1.4_R0.pdf)

[^laz_standard]:
    [OGC considering LAZ v1.4 – R1 as a Community Standard](https://www.ogc.org/requests/laz-v1-4-community-standard-work-item-proposal/)

[^las_specification]:
    [LAS Specification](https://www.asprs.org/wp-content/uploads/2019/07/LAS_1_4_r15.pdf)

# Compressão _DEFLATE_ em arquivos `.tif`

O formato de imagem TIFF é geralmente considerado o padrão de ouro para
armazenamento a longo prazo de dados de imagens [^tiff_compression]. Sua
principal desvantagem é o tamanho ocupado, porém podem ser empregadas técnicas
de compressão para a sua redução.

O formato GeoTIFF é um padrão onde são inseridas informações de
georreferenciamento nos metadados de uma imagem TIFF [^geotiff].

O algoritmo de compressão _DEFLATE_ (compressão ZIP) possui a vantagem de
reduzir o tamanho ocupado em até 80% [^tiff_compression], sem perca de
qualidade, por ser um algoritmo de compressão sem perdas, e mantendo
compatibilidade com a maioria dos softwares de geoprocessamento.

Em um dado real de batimetria o tamanho do arquivo foi de 10.2 MB para 3.5 MB,
após a compressão, uma redução de 66%.

No software de GIS Global Mapper é possível exportar arquivos tiff com
compressão:

{:style="text-align:center;"}
![Exportar TIFF com compressão no software Global Mapper](./assets/images/global-mapper-tiff-compression.png)

Para a compressão de arquivos no formato `.tif`, o software
`Lastools Integration` pode ser utilizado:

{:style="text-align:center;"}
![Lastools Integration compressão de TIFF](./assets/images/lastools-compress-tiff.png)

[^tiff_compression]:
    [TIFF compression? Use discretion!](https://openpreservation.org/blogs/compression-at-your-discretion/)

[^geotiff]: [GeoTIFF](https://en.wikipedia.org/wiki/GeoTIFF)

# Compressão de arquivos para arquivamento em `.7z`

O formato de arquivo `.7z` (7-Zip) é um formato de compressão de arquivos que
utiliza o software 7-Zip de código aberto e totalmente gratuito e que faz uso
de um algoritmo de compressão mais eficiente que os utilizados nos formatos
`.zip` e `.rar`, na maioria das vezes gerando arquivos menores e fazendo melhor
uso de multithread [^7zip].

Uma tabela comparativa de compressão de arquivos utilizando os softwares 7-Zip
e WinRar, utilizando os métodos com maior compressão:

| Pasta original | `.7z` com 7-Zip | `.zip` com 7-Zip | `.rar` com WinRar | `.zip` com WinRar |
| :------------: | :-------------: | :--------------: | :---------------: | :---------------: |
|     860 MB     |    102.1 MB     |     148.1 MB     |     154.4 MB      |     165.5 MB      |

Nota-se melhor eficiência no formato `.7z` com 7-Zip. Até mesmo na utilização
do formato `.zip` o software 7-Zip apresentou melhor eficiência.

Para a utilização do software 7-Zip, com o método de melhor compressão, basta
modificar o `Nível de compressão` para `9 - Ultra`, os outros parâmetros são
ajustados automaticamente:

{:style="text-align:center;"}
![Compactação de arquivos com 7-Zip](./assets/images/compress-7z.png)

[^7zip]: [7-Zip](https://www.7-zip.org/)

# Compressão transparente no sistema de arquivos NTFS

A compressão transparente no sistema de arquivos NTFS pode reduzir o tamanho de
arquivos em até 60%, especialmente arquivos de texto [^ntfs_compression_1].
Esse tipo de compressão ocorre a nível de sistema de arquivos, comprimindo
blocos, sendo os blocos descomprimidos durante operações de leitura.

A compressão ocorre a cada escrita e a descompressão a cada leitura. Há então
uso de CPU a cada operação. Porém, geralmente, o custo de operações de leitura
e escrita são maiores que os de compressão e descompressão, o que pode melhorar
o desempenho geral, especialmente em dispositivos em rede
[^ntfs_compression_2].

Para se habilitar a compressão transparente no sistema de arquivos NTFS, basta
abrir o painel de propriedades do arquivo/pasta, clicar em `Avançados...` na
parte de `Atributos`, e selecionar
`Compactar o conteúdo para economizar espaço em disco` na parte de
`Atributos de compactação e Codificação`:

{:style="text-align:center;"}
![Propriedades](./assets/images/windows-compression-1.png)

{:style="text-align:center;"}
![Atributos avançados](./assets/images/windows-compression-2.png)

É possível também habilitar a compressão transparente em todo o disco, abrindo
o painel de propriedades do disco e clicando em
`Compactar este disco para economizar espaço`:

{:style="text-align:center;"}
![Propriedades de disco](./assets/images/windows-compression-3.png)

[^ntfs_compression_1]:
    [Disk Space Cleanup Guidance: Using NTFS Compression](https://www.foldersizes.com/wordpress/index.php/2015/01/disk-space-cleanup-tip-ntfs-compression.htm)

[^ntfs_compression_2]:
    [Pros and Cons of Enabling File and Folder Compression](https://www.itechtics.com/ntfs-compression/)
