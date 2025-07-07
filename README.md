**Requirements Document: Reusable Map Block**

---

## 1. Overview

**Objective:** Develop a reusable map block for Adobe Franklin that enables displaying multiple sets of locations (e.g., vaccination centers, oncology hospitals), configurable via self-service JSON files.

**Scope:**

* Create a map component importable into any Franklin page.
* Support single or multiple external JSON files as location data sources.
* Allow dynamic configuration of which dataset each block instance displays.

---

## 2. Stakeholders

* **Web Development Team:** Implements and maintains the map block.
* **Content Team:** Creates and updates JSON files with location data.
* **End Users:** Patients or general public accessing points of interest.

---

## 3. Functional Requirements

| ID  | Description                                                                                   |
| --- | --------------------------------------------------------------------------------------------- |
| RF1 | The block must be insertable on any Adobe Franklin page as a reusable component.              |
| RF2 | Accept one or more JSON file URLs and allow selecting which dataset to display per instance.  |
| RF3 | Display a map centered on a configurable default coordinate (latitude, longitude).            |
| RF4 | Render markers from the JSON, displaying name, address, and phone for each location.          |
| RF5 | Provide address search and a proximity filter with configurable radius.                       |
| RF6 | Each block instance must specify its own locations set via JSON URLs or a category parameter. |
| RF7 | Handle JSON load errors (e.g., 404 or parse failures) by showing a user-friendly message.     |
| RF8 | Allow style customization (marker colors, map dimensions) via block parameters.               |

---

## 4. Non-Functional Requirements

* **Performance:** Map load time under 1 second on 4G connections.
* **Responsiveness:** Fluid adaptation across mobile, tablet, and desktop screen sizes.
* **Accessibility:** Support keyboard navigation and include ARIA labels for markers.
* **Security:** Sanitize JSON input to prevent XSS vulnerabilities.
* **Maintainability:** Well-documented, modular code for easy future enhancements.

---

## 5. Location JSON Schema

```json
{
  "locations": [
    {
      "id": "string",
      "name": "Location Name",
      "lat": number,
      "lng": number,
      "address": "Address",
      "phone": "Optional phone",
      "category": "vaccination" // or "oncology"
    }
    // ... more entries
  ]
}
```

* Block accepts an array of JSON URLs.
* Content team can self-publish or update these files in a public storage.

---

## 6. Adobe Franklin Integration

* **Component usage:**

  ```jsx
  <MapBlock
    src={["/data/vaccination.json", "/data/oncology.json"]}
    category="vaccination"
    center={[-34.6037, -58.3816]}
    zoom={12}
    markerColor="blue"
    radiusDefault={2}
  />
  ```

* **Configurable props:**

  * `src` (string or array): JSON file URL(s).
  * `category` (string, optional): Filter to show only locations in the specified category.
  * `center` (array of two numbers): Initial map coordinates \[lat, lng].
  * `zoom` (number): Initial zoom level.
  * `markerColor` (string): Marker color.
  * `radiusDefault` (number): Default radius in kilometers for proximity filter.

* **Import in Franklin template:**

  ```jsx
  import MapBlock from '../components/MapBlock';
  ```

---

## 7. Data Flow and Error Handling

1. On render, the component fetches each JSON via `fetch`.
2. If a fetch fails or JSON parsing errors occur, display a friendly error message.
3. On success, parse and store locations.
4. Initialize the chosen map library (e.g., Google Maps or Leaflet) and render markers.
5. User actions (search or radius change) recalculate distances and update the UI and results list.

---

## 8. Testing and Validation

* **Unit Tests:** Validate JSON fetching, parsing, and error scenarios.
* **End-to-End Tests:** Deploy on a Franklin page and test with various JSON files.
* **Accessibility Audit:** Use Lighthouse and screen readers (e.g., NVDA).
* **Cross-Browser Verification:** Test on Chrome, Firefox, and Safari (desktop and mobile).

---

## 9. Maintenance and Documentation

* Provide a README in the component repository detailing props, JSON schema, and examples.
* Use semantic versioning and maintain a CHANGELOG.md.

---

## 10. Tracking & Analytics

* **Goal:** Measure usage and effectiveness of the map block.

* **Events to capture:**

  1. `mapSearch` when a user searches, with parameters:

     * `query`: search term
     * `radius`: selected radius (km)
     * `instanceId`: unique ID for the block instance
  2. `mapResults` when results are listed, with:

     * `resultCount`: number of locations returned
     * `query`, `radius`, `instanceId`
  3. `mapMarkerClick` when a marker is clicked, with:

     * `locationId`: ID of the clicked location
     * `instanceId`

* **Implementation:**

  * Integrate with Adobe Analytics using `satellite.track` or `AA.sendBeacon`.
  * Generate a unique `instanceId` (e.g., UUID or timestamp) for each block instance.

---

*Last updated: July 7, 2025*
