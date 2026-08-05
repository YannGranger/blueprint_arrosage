# blueprint_arrosage

Blueprint Home Assistant pour l'arrosage automatique : se déclenche une fois
par jour à heure fixe, annule si de la pluie a été détectée (intégration
Météo-France), sinon applique une durée d'arrosage selon 2 paliers de
température extérieure.

**Logique :**
- Pluie détectée (cumul ≥ seuil configuré) → arrosage annulé
- Température < palier 1 → durée courte
- Palier 1 ≤ température ≤ palier 2 → durée moyenne
- Température > palier 2 → durée longue

## Fichiers

- `arrosage_automatique_blueprint.yaml` — le blueprint de l'automation
- `arrosage_automatique_helpers.yaml` — les `input_number` / `input_datetime`
  / `input_boolean` nécessaires (seuils, durées, heure de déclenchement,
  notifications)

## Installation

### 1. Créer les helpers

Deux méthodes au choix pour `arrosage_automatique_helpers.yaml` :

**a) Package (recommandé)**

1. Copiez `arrosage_automatique_helpers.yaml` dans votre dossier config HA,
   par exemple dans `packages/`.
2. Activez les packages dans `configuration.yaml` si ce n'est pas déjà fait :
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
3. Redémarrez Home Assistant.

**b) Fusion manuelle**

Copiez le contenu des sections `input_number:`, `input_datetime:` et
`input_boolean:` du fichier dans les sections correspondantes de votre
`configuration.yaml` (fusionnez si elles existent déjà), puis redémarrez.

Cela crée les entités suivantes :

| Entité | Rôle |
|---|---|
| `input_number.arrosage_seuil_pluie_mm` | Seuil de pluie (mm) au-delà duquel l'arrosage est annulé |
| `input_number.arrosage_palier_temp_1` | Palier de température 1 (°C) |
| `input_number.arrosage_palier_temp_2` | Palier de température 2 (°C) |
| `input_number.arrosage_duree_froid` | Durée d'arrosage si température < palier 1 (min) |
| `input_number.arrosage_duree_moyen` | Durée d'arrosage si température entre palier 1 et 2 (min) |
| `input_number.arrosage_duree_chaud` | Durée d'arrosage si température > palier 2 (min) |
| `input_datetime.arrosage_heure_declenchement` | Heure de déclenchement quotidien |
| `input_boolean.arrosage_activer_notifications` | Active/désactive les notifications |

### 2. Importer le blueprint

1. Dans Home Assistant : **Paramètres → Automatisations et scènes → Blueprints
   → Importer un blueprint**.
2. Collez l'URL brute de `arrosage_automatique_blueprint.yaml` sur GitHub
   (bouton "Raw" sur la page du fichier), ou copiez son contenu directement
   dans `config/blueprints/automation/arrosage_automatique_blueprint.yaml`.
3. Redémarrez Home Assistant ou rechargez les blueprints.

### 3. Créer l'automation

1. **Paramètres → Automatisations et scènes → Créer une automation → À
   partir d'un blueprint → Arrosage Automatique**.
2. Renseignez les champs :
   - **Capteur température extérieure** : votre capteur de température
   - **Capteur cumul de pluie** : capteur de cumul de pluie de l'intégration
     Météo-France
   - **Seuil de pluie / Paliers de température / Durées** : les
     `input_number` créés à l'étape 1
   - **Heure de déclenchement** : `input_datetime.arrosage_heure_declenchement`
   - **Switch / électrovanne d'arrosage** : votre switch d'arrosage
   - **Service de notification** : ex. `notify.mobile_app_xxx`
   - **Activer les notifications** : `input_boolean.arrosage_activer_notifications`
3. Enregistrez.

L'automation se déclenchera chaque jour à l'heure configurée, vérifiera la
pluie et la température, puis arrosera (ou non) selon la logique décrite
ci-dessus.
