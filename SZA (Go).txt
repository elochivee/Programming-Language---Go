package main

import (
        "fmt"
        "html/template"
        "math/rand"
        "net/http"
        "time"
)

// Define moods and songs
var moodSongs = map[string][]string{
        "happy":    {"Good Days", "Prom", "Broken Clocks"},
        "sad":      {"Supermodel", "Drew Barrymore", "20 Something"},
        "heartbroken": {"Nobody Gets Me", "Special", "Normal Girl"},
        "chill":    {"Love Galore", "The Weekend", "Go Gina"},
        "angry":    {"Kill Bill", "Shirt", "Smoking on My Ex Pack"},
        "confident": {"SOS", "Conceited", "Low"},
}

ar tmpl = template.Must(template.New("webpage").Parse(`
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SZA Song Generator</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; padding: 50px; }
        button { padding: 10px 20px; margin: 5px; font-size: 16px; cursor: pointer; }
        .song-box { margin-top: 20px; font-size: 20px; font-weight: bold; }
    </style>
</head>
<body>
<h1>How are you, hooman?</h1>
    <form method="POST">
        <button name="mood" value="happy">Happy </button>
        <button name="mood" value="sad">Sad </button>
        <button name="mood" value="heartbroken">Heartbroken </button>
        <button name="mood" value="chill">Chill </button>
        <button name="mood" value="angry">Angry </button>
        <button name="mood" value="confident">Confident </button>
    </form>
    
    {{if .Song}}
    <div class="song-box">
        <p>🎶 Recommended SZA Song for {{.Mood}}: <strong>{{.Song}}</strong></p>
    </div>
    {{end}}
</body>
</html>
`))

func songHandler(w http.ResponseWriter, r *http.Request) {
        if r.Method == "POST" {
                mood := r.FormValue("mood")
                songs, exists := moodSongs[mood]
                if exists {
                        rand.Seed(time.Now().UnixNano())
                        song := songs[rand.Intn(len(songs))]
                        tmpl.Execute(w, map[string]string{"Mood": mood, "Song": song})
                        return
                }
        }
        tmpl.Execute(w, nil)
}

func main() {
        http.HandleFunc("/", songHandler)
        fmt.Println("Server running on http://localhost:8080/")
        http.ListenAndServe(":8080", nil)
}
