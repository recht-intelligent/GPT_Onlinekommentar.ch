# GPT_Onlinekommentar.ch
Ein simpler GPT um Onlinekommentar.ch zu durchsuchen.

## Prompts
Hier ein grober Prompt für den GPT:
```
Du bist ein juristischer Rechercheprofi mit exklusivem Zugriff auf die API von onlinekommentar.ch.
Deine Aufgabe ist es, immer eine Recherche über die API-Endpunkte durchzuführen, bevor Du antwortest.

Wichtige Regeln:

Zwingend:

Du musst mindestens einen API-Call mit /api/commentaries durchführen.

Wenn die Suchergebnisse nicht ausreichen oder unklar sind, führe mehrere Abfragen nacheinander durch.

Um Details oder den Volltext zu erhalten, hole zusätzlich mit /api/commentaries/{id} den spezifischen Kommentar ab.

Wenn keine Antwort möglich ist:

Falls die Quellen keine relevanten Informationen liefern, antworte strikt: „Ich weiss es nicht.“

Quellenangabe:

Jede Antwort muss die Quelle angeben.

Verwende dabei den offiziellen Link im Format:
https://onlinekommentar.ch/de/kommentare/{id}

Endpunkte:

GET /api/commentaries – Listet veröffentlichte Kommentare

Parameter:

language – en, de, fr, it (Default: en)

search – Volltextsuche

legislative_act – Filter nach Gesetzes-ID

sort – title, -title, date, -date (Default: -date)

page – Seitenzahl (Default: 1)

GET /api/commentaries/{id} – Ruft einen spezifischen Kommentar per ID ab

Vorgehen Schritt für Schritt:

Immer zuerst GET /api/commentaries mit einem passenden Suchbegriff aufrufen.

Falls nötig, weitere Suchanfragen (z. B. mit angepasstem Suchbegriff oder Parametern).

Wenn ein relevanter Treffer vorliegt, immer den Volltext mit GET /api/commentaries/{id} nachladen.

Formuliere Deine Antwort auf Basis der Quellen.

Verlinke die Quelle(n) korrekt.
```

## OpenAPI Specification für onlinekommentar.ch API
```openapi
{
  "openapi": "3.1.0",
  "info": {
    "title": "Onlinekommentar API",
    "description": "API for accessing commentary data from the Onlinekommentar platform",
    "version": "1.0.0",
    "contact": {
      "url": "https://onlinekommentar.ch"
    }
  },
  "servers": [
    {
      "url": "https://onlinekommentar.ch/api",
      "description": "Production server"
    }
  ],
  "paths": {
    "/commentaries": {
      "get": {
        "summary": "List all published commentaries",
        "description": "Retrieve a list of all published commentaries with optional filtering, searching, and sorting",
        "operationId": "listCommentaries",
        "parameters": [
          {
            "name": "language",
            "in": "query",
            "description": "Content language",
            "required": false,
            "schema": {
              "type": "string",
              "enum": ["en", "de", "fr", "it"],
              "default": "en"
            }
          },
          {
            "name": "search",
            "in": "query",
            "description": "Full-text search query",
            "required": false,
            "schema": {
              "type": "string"
            }
          },
          {
            "name": "legislative_act",
            "in": "query",
            "description": "Filter by legislative act ID",
            "required": false,
            "schema": {
              "type": "string",
              "format": "uuid"
            }
          },
          {
            "name": "sort",
            "in": "query",
            "description": "Sort order (prefix with - for descending)",
            "required": false,
            "schema": {
              "type": "string",
              "enum": ["title", "-title", "date", "-date"],
              "default": "-date"
            }
          },
          {
            "name": "page",
            "in": "query",
            "description": "Page number for pagination",
            "required": false,
            "schema": {
              "type": "integer",
              "minimum": 1,
              "default": 1
            }
          }
        ],
        "responses": {
          "200": {
            "description": "Successful response",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "commentaries": {
                      "type": "array",
                      "items": {
                        "$ref": "#/components/schemas/Commentary"
                      }
                    }
                  }
                }
              }
            }
          },
          "400": {
            "description": "Bad request - invalid parameters"
          },
          "500": {
            "description": "Internal server error"
          }
        }
      }
    },
    "/commentaries/{id}": {
      "get": {
        "summary": "Retrieve a specific commentary by ID",
        "description": "Get detailed information about a specific commentary",
        "operationId": "getCommentaryById",
        "parameters": [
          {
            "name": "id",
            "in": "path",
            "description": "Commentary ID",
            "required": true,
            "schema": {
              "type": "string",
              "format": "uuid"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "Successful response",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Commentary"
                }
              }
            }
          },
          "404": {
            "description": "Commentary not found"
          },
          "500": {
            "description": "Internal server error"
          }
        }
      }
    }
  },
  "components": {
    "schemas": {
      "Commentary": {
        "type": "object",
        "description": "A legal commentary document",
        "properties": {
          "id": {
            "type": "string",
            "format": "uuid",
            "description": "Unique identifier for the commentary"
          },
          "title": {
            "type": "string",
            "description": "Title of the commentary"
          },
          "date": {
            "type": "string",
            "format": "date",
            "description": "Publication date"
          },
          "language": {
            "type": "string",
            "enum": ["en", "de", "fr", "it"],
            "description": "Language of the commentary"
          },
          "legislative_act": {
            "type": "string",
            "format": "uuid",
            "description": "Associated legislative act ID"
          }
        }
      }
    }
  }
}
```
