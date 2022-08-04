# RSD Graph

Trying to visualize data from the [research software directory](https://research-software-directory.org) in the form of an (interactive) network graph.

![network_graph_mwe](https://user-images.githubusercontent.com/17080502/182891021-9f12a247-c570-4cc7-924c-8cc0b1e5fa0f.png)

Or see a live preview via githack: https://raw.githack.com/Peter9192/rsd-graph/main/index.html


## RSD API

- The RSD API is documented at https://research-software-directory.github.io/RSD-as-a-service/api.html
- The API is accessible at https://research-software-directory.org/api/v1
- For some examples of how it's used, I looked at the RSD frontend code for [software index page](https://github.com/research-software-directory/RSD-as-a-service/blob/0640d11c5283187f822d0600ed9eab36cb8658cc/frontend/pages/software/index.tsx), [software details page](https://github.com/research-software-directory/RSD-as-a-service/blob/0640d11c5283187f822d0600ed9eab36cb8658cc/frontend/pages/software/%5Bslug%5D/index.tsx), and [contributor utilities](https://github.com/research-software-directory/RSD-as-a-service/blob/0640d11c5283187f822d0600ed9eab36cb8658cc/frontend/utils/editContributors.ts)

Examples:

- To get the ID and slug of the first 12 software entries: `curl "https://research-software-directory.org/api/v1/software?select=id,slug&limit=12"`
- To find the ID of ewatercycle: `curl "https://research-software-directory.org/api/v1/software?select=id,slug&slug=eq.ewatercycle"`
- To get the contributors to ewatercycle: `curl "https://research-software-directory.org/api/v1/contributor?select=given_names,family_names&software=eq.88ccae17-87b0-4dab-aa26-346aad7ca424"`

## Graph visualization

There are many [ways, tools, packages](https://elise-deux.medium.com/the-list-of-graph-visualization-libraries-7a7b89aab6a6) to generate graphs. I want something that runs in the browser and can be explored interactively. Ideally the interaction is not limited to zooming and panning, but also e.g. refocusing on a node or expanding it when it is (double?) clicked. An example of such functionality can be found [here](https://ialab.it.monash.edu/webcola/examples/browsemovies.html). This seems beyond the standard use case/gallery example of most packages though, and I think it's probably easiest to implement this by simply adding callbacks that update the data.

Some basic examples:
Here's a simple implementation in Vega: https://vega.github.io/vega/examples/force-directed-layout/, based on [D3-force](https://github.com/d3/d3-force)

([React-](https://sim51.github.io/react-sigma/docs/start-introduction/))[sigma](https://www.sigmajs.org/) seems interesting as well, but users complain about a lack of documentation.

[Cytoscape](https://cytoscape.org/what_is_cytoscape.html ) is a desktop app but it also has a javascript brother now: https://js.cytoscape.org/

[Force-graph example](https://vasturiano.github.io/react-force-graph/example/expandable-nodes/) with expandable nodes.

## Force graph
Eventually I decided to start experimenting with [force-graph](https://github.com/vasturiano/force-graph), which seems quite simple, has a nice quickstart example, and also has a 3D version levering WebGL and react bindings.

It was easy enough to get a very minimal example up and running. The data structure can be as simple as

```json
{
  "nodes": [
    {"id": "id1", "name": "name1", "val": 1},
    {"id": "id2", "name": "name2", "val": 10},
  ],
  "links": [
    {"source": "id1", "target": "id2"},
  ]
}
```

so making a basic graph of the RSD will require fetching a number of contributors, a number of software/projects, and the links between contributors and software/projects.

I very quickly hacked something together in index.html.
