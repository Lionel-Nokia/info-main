# Commande extraction vidéo macOS

**User:** Anonymous  
**Created:** 8/6/2026 7:29:35  
**Updated:** 8/6/2026 7:34:13  
**Exported:** 8/6/2026 7:37:34  
**Link:** [https://chatgpt.com/c/6a741ba4-43cc-83ed-bde7-01a6b25d646e](https://chatgpt.com/c/6a741ba4-43cc-83ed-bde7-01a6b25d646e)  

## Response:
06/08/2026 07:30:41

Oui. Avec [`yt-dlp`](https://github.com/yt-dlp/yt-dlp), pour une vidéo que tu as le droit de télécharger :

```bash
brew install yt-dlp ffmpeg
yt-dlp -f "bv*+ba/b" --merge-output-format mp4 "URL_YOUTUBE"
```

Le fichier sera enregistré dans le dossier courant.

Pour choisir le dossier et conserver le titre :

```bash
yt-dlp -P "$HOME/Downloads" -o "%(title)s.%(ext)s" -f "bv*+ba/b" --merge-output-format mp4 "URL_YOUTUBE"
```

Remplace `URL_YOUTUBE` par l’adresse de la vidéo. Respecte les droits d’auteur et les conditions d’utilisation de YouTube.

## Response:
06/08/2026 07:31:19

Non, je ne peux pas aider à télécharger une vidéo sans l’autorisation du détenteur des droits.

Options légales :

- utiliser le bouton **Télécharger** de YouTube Premium pour une lecture hors connexion ;
- demander l’autorisation au créateur ;
- chercher une version sous licence Creative Commons ;
- télécharger tes propres vidéos via YouTube Studio ou Google Takeout.

`yt-dlp` convient si la vidéo est à toi, librement licenciée, dans le domaine public, ou si tu as reçu l’autorisation.



---
Powered by [ChatGPT Exporter](https://www.chatgptexporter.com)