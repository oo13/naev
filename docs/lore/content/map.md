---
title: Star Map
---
<% content_for :javascript do %>
<script src="<%= relative_path_to(@items['/js/sigma.js']) %>"></script>
<script src="<%= relative_path_to(@items['/js/graphology.umd.js']) %>"></script>
<script>
    // Create a graphology graph
    const graph = new graphology.Graph();

    var ssys = [<%= out = ""
    @items.find_all('/ssys/*.md').each do |s|
        out += "{ name:\"#{s[:name]}\", x:#{s[:x]}, y:#{s[:y]} },\n"
    end
    out %>];
    var n = ssys.length;
    for (var i=0; i<n; i++) {
        var s = ssys[i];
        graph.addNode( s.name, { label: s.name, x: s.x, y: s.y, size: 5, color: "white", borderColor: "white" } );
    }
    var jumps = [<%= out = ""
    @items.find_all('/ssys/*.md').each do |s|
        s[:jumps].each do |j|
            if s[:name] < j[:target]
                out += "{ a:\"#{s[:name]}\", b:\"#{j[:target]}\", h:#{j[:hidden]} },\n"
            end
        end
    end
    out %>];
    var nj = jumps.length;
    for (var i=0; i<nj; i++) {
        var j = jumps[i];
        graph.addEdge( j.a, j.b, { size: 2, color: (j.h) ? 'red' :'blue' } );
    }

    // Instantiate sigma.js and render the graph
    const sigmaInstance = new Sigma( graph, document.getElementById("starmap"), {
        labelColor: { color: "white" },
        //doubleClickEnabled: false,
        //defaultNodeType: "bordered",
        //nodeProgramClasses: {
        //    bordered: NodeBorderProgram,
        //},
    } );
    sigmaInstance.on('doubleClickNode', function(event) {
        const ssysModal = new bootstrap.Modal('div.modal[data-Name="'+event.node+'"]');
        ssysModal.show();
        event.preventSigmaDefault();
    });
</script>
<% end %>

<div id="starmap" style="width: 100%; height: 600px; background: black"></div>

<%= modal_addAll() %>