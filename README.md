#**Introdução ao Youtool**

**(Youtool é uma biblioteca e CLI que facilita o acesso a dados de vídeos do YouTube)**

Exemplos que utilização da biblioteca Youtool. Os exemplos foram extraídos da apresentação realizada na Python Brasil 2024 (19/10/2024) por Álvaro Justen & Ana Paula Sales.

Para mais informações acesse o GitHub do projeto em: https://github.com/PythonicCafe/youtool

### [1] Instalação da biblioteca: Youtool


```python
!pip install youtool
```

### [2] Instalações extras da biblioteca: Youtool


```python
!pip install youtool[livechat]
!pip install youtool[transcription]
```

### [3] Descobrindo IDs de canais:


```python
#Não precisa de chave da API!
import os
from youtool import YouTube
from google.colab import userdata

# Create one in Google Cloud Console
api_keys = [userdata.get('YOUTUBE_API_KEY')]

#Você pode passar mais de uma chave
yt = YouTube(api_keys)

url = "https://www.youtube.com/@Joniel-Youtube"
#Scraping
channel_id = yt.channel_id_from_url(url)
print(f"ID do canal: {channel_id}")

```

### [4] Listando playlists de um canal:


```python
#Necessita de uma chave de API do YouTube (gratuita)
from pprint import pprint
info = list(yt.channels_infos([channel_id]))[0]
print("Channel info:")
pprint(info)
playlist_id = info["playlist_id"]
print(f"Playlist ID: {playlist_id}")

```

### [5] Listando vídeos de uma playlist:


```python
import datetime
videos_ids = []
total_videos = 0
momento_inicial = datetime.datetime(2024, 1, 1, 3, 0, 0, tzinfo=datetime.timezone.utc)
for video in yt.playlist_videos(playlist_id):
  if video["published_at"] >= momento_inicial:
    videos_ids.append(video["id"])
  total_videos += 1
print(f"Identificados {len(videos_ids)} vídeos de um total de {total_videos} nessa playlist")

```

### [6] Detalhes de um vídeo:


```python
# O método `playlist_videos` não nos fornece todas as informações de um vídeo
videos = list(yt.videos_infos(videos_ids))
print("Informações do primeiro vídeo:")
pprint(videos[0])

```

### [7] Criar a pasta "transcricoes":


```python
import os

# Defina o nome da nova pasta
nome_da_pasta = 'transcricoes'

# Crie a pasta
os.makedirs(nome_da_pasta, exist_ok=True)

print(f"Pasta '{nome_da_pasta}' criada com sucesso!")

```

### [8] Baixando transcrição/áudio/vídeo:


```python
print(videos_ids)
# Troque por `download_audios` ou `download_videos`
for status in yt.download_transcriptions(
    videos_ids=videos_ids, language_code="pt", path="/content/transcricoes", batch_size=3,
):
  print(status)

```

### [9] Trabalhando com as transcrições:


```python
from youtool.utils import simplify_vtt

input_filename = f"/content/transcricoes/{videos_ids[0]}.pt.vtt"
output_filename = f"/content/transcricoes/{videos_ids[0]}_simplified.txt"

with open(filename) as fobj:
  vtt = fobj.read()
simplified = simplify_vtt(vtt)

# Salvar a saída em um arquivo .txt
with open(output_filename, 'w') as outfile:
    for line in simplified.splitlines():
        outfile.write(f"{line.strip()}\n")

```
