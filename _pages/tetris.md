---
permalink: /tetris
title: "Tetris"
author_profile: false
toc: true
---

# some Tetris Stuff

  <div id="graph"></div>

  <script src="//unpkg.com/force-graph"></script>
  <script src="//unpkg.com/d3-quadtree"></script>
  <script src="//unpkg.com/d3-force"></script>

  <script>
    function mirrorField(board) {
      var rows = board.split('|')
      for(var i in rows) {
        var r = rows[i]
        rows[i] = r.split('').reverse().join('')
      }
      var mirroed = rows.join('|')
      return mirroed.replace(/s/g, 'Z').replace(/z/g, 'S').replace(/j/g, 'L').replace(/l/g, 'J').toLowerCase()
    }
    const transitions_half = [
      { start: '....|....|gg..|g...', play: '....|...t|ggtt|g..t', end: '....|....|...g|g..g' },
      { start: '....|....|gg..|g...', play: '....|....|ggt.|gttt', end: '....|....|....|ggg.' },
      { start: '....|....|gg..|g...', play: '....|....|gg.l|glll', end: '....|....|....|gg.g' },
      { start: '....|....|gg..|g...', play: '....|....|ggss|gss.', end: '....|....|....|ggg.' },
      { start: '....|....|gg..|g...', play: '....|..s.|ggss|g..s', end: '....|....|..g.|g..g' },
      { start: '....|....|gg..|g...', play: '....|...z|ggzz|g.z.', end: '....|....|...g|g.g.' },
      { start: '....|....|gg..|g...', play: '....|.zz.|ggzz|g...', end: '....|....|.gg.|g...' },
      { start: '....|....|gg..|g...', play: '....|....|ggoo|g.oo', end: '....|....|....|g.gg' },
      
      { start: '....|....|g...|g..g', play: '....|....|gttt|g.tg', end: '....|....|....|g.gg' },
      { start: '....|....|g...|g..g', play: '....|..t.|gttt|g..g', end: '....|....|..g.|g..g' },
      { start: '....|....|g...|g..g', play: '....|...l|glll|g..g', end: '....|....|...g|g..g' },
      { start: '....|....|g...|g..g', play: '....|....|glll|gl.g', end: '....|....|....|gg.g' },
      { start: '....|....|g...|g..g', play: '....|.l..|gl..|gllg', end: '....|....|.g..|gg..' },
      { start: '....|....|g...|g..g', play: '....|.j..|gjjj|g..g', end: '....|....|.g..|g..g' },
      { start: '....|....|g...|g..g', play: '....|....|g.zz|gzzg', end: '....|....|....|g.gg' },
      { start: '....|....|g...|g..g', play: '....|....|goo.|goog', end: '....|....|....|ggg.' },
   
      { start: '....|....|g...|gg..', play: '....|....|gttt|ggt.', end: '....|....|....|ggg.' },
      { start: '....|....|g...|gg..', play: '....|...l|glll|gg..', end: '....|....|...g|gg..' },
      { start: '....|....|g...|gg..', play: '....|...j|g..j|ggjj', end: '....|....|...g|g..g' },
      { start: '....|....|g...|gg..', play: '....|....|gjjj|gg.j', end: '....|....|....|gg.g' },
      { start: '....|....|g...|gg..', play: '....|.j..|gjjj|gg..', end: '....|....|.g..|gg..' },
      { start: '....|....|g...|gg..', play: '....|....|gzz.|ggzz', end: '....|....|....|ggg.' },
      { start: '....|....|g...|gg..', play: '....|....|g.oo|ggoo', end: '....|....|....|g.gg' },

      { start: '....|....|....|ggg.', play: '....|...t|..tt|gggt', end: '....|....|...g|..gg' },
      { start: '....|....|....|ggg.', play: '....|..ll|...l|gggl', end: '....|....|..gg|...g' },
      { start: '....|....|....|ggg.', play: '....|....|.jjj|gggj', end: '....|....|....|.ggg' },
      { start: '....|....|....|ggg.', play: '....|..s.|..ss|gggs', end: '....|....|..g.|..gg' },
      { start: '....|....|....|ggg.', play: '...i|...i|...i|gggi', end: '....|...g|...g|...g' },

      { start: '....|g...|g...|g...', play: '....|g...|g.t.|gttt', end: '....|....|g...|g.g.' },
      { start: '....|g...|g...|g...', play: '....|g...|g..l|glll', end: '....|....|g...|g..g' },
      { start: '....|g...|g...|g...', play: '....|g...|glll|gl..', end: '....|....|g...|gg..' },
      { start: '....|g...|g...|g...', play: '....|g...|gjjj|g..j', end: '....|....|g...|g..g' },
      { start: '....|g...|g...|g...', play: '....|g...|gj..|gjjj', end: '....|....|g...|gg..' },

      { start: '....|....|....|gg.g', play: '....|....|.ttt|ggtg', end: '....|....|....|.ggg' },
      { start: '....|....|....|gg.g', play: '....|..t.|..tt|ggtg', end: '....|....|..g.|..gg' },
      { start: '....|....|....|gg.g', play: '....|....|jjj.|ggjg', end: '....|....|....|ggg.' },
      { start: '....|....|....|gg.g', play: '....|..jj|..j.|ggjg', end: '....|....|..gg|..g.' },
      { start: '....|....|....|gg.g', play: '....|...z|..zz|ggzg', end: '....|....|...g|..gg' },
      
      { start: '....|....|...g|gg..', play: '....|.t..|tttg|gg..', end: '....|....|.g..|gg..' },
      { start: '....|....|...g|gg..', play: '....|j...|jjjg|gg..', end: '....|....|g...|gg..' },
      { start: '....|....|...g|gg..', play: '....|....|jjjg|ggj.', end: '....|....|....|ggg.' },
      { start: '....|....|...g|gg..', play: '....|....|.zzg|ggzz', end: '....|....|....|.ggg' },

      { start: '....|....|gg..|.g..', play: '....|...t|ggtt|.g.t', end: '....|....|...g|.g.g' },
      { start: '....|....|gg..|.g..', play: '....|...z|ggzz|.gz.', end: '....|....|...g|.gg.' },
      { start: '....|....|gg..|.g..', play: '....|....|ggoo|.goo', end: '....|....|....|.ggg' },

      { start: '....|....|g...|g.g.', play: '....|....|glll|glg.', end: '....|....|....|ggg.' },
      { start: '....|....|g...|g.g.', play: '....|...l|glll|g.g.', end: '....|....|...g|g.g.' },
      { start: '....|....|g...|g.g.', play: '....|....|gjjj|g.gj', end: '....|....|....|g.gg' },
 
      { start: '....|....|.g..|gg..', play: '....|...j|.g.j|ggjj', end: '....|....|...g|.g.g' },
      { start: '....|....|.g..|gg..', play: '....|....|.goo|ggoo', end: '....|....|....|.ggg' },
      
      { start: '....|....|g...|.gg.', play: '....|...l|glll|.gg.', end: '....|....|...g|.gg.' },
      { start: '....|....|g...|.gg.', play: '....|....|gjjj|.ggj', end: '....|....|....|.ggg' },
      
      { start: '....|....|g...|.g.g', play: '....|....|gttt|.gtg', end: '....|....|....|.ggg' },
      { start: '....|....|g...|.g.g', play: '....|...l|glll|.g.g', end: '....|....|...g|.gg.' },
      
      { start: '....|....|.g..|g..g', play: '....|....|.gss|gssg', end: '....|....|....|.ggg' }, 
      
      { start: '....|....|.gg.|g...', play: '....|....|.ggj|gjjj', end: '....|....|....|.ggg' },
    ];
    var transitions = [];
    for(var t of transitions_half){
      const mstart = mirrorField(t.start);
      const mplay = mirrorField(t.play);
      const mend = mirrorField(t.end);
      transitions.push(t, {start: mstart, play: mplay, end: mend});
    }
    var boards = [];
    var links = [];
    for( var t of transitions ) {
       boards.push(t.start, t.play, t.end);
       links.push(
         {source: t.start, target: t.play},
         {source: t.play, target: t.end}
       )
    }
     
    const gData = { 
      nodes: [...new Set(boards)].map(x => new Object({id: x}) ),
      links: links
    };
    
    function drawTetrisField(board, pos_x, pos_y, ctx) {
      const sz = 5;
      const pad = 0.1*sz;
      const off = sz*2;
      var x=0, y=0;
      for(var c of board) {
        if(c=='|') continue;
        else if(c=='.') color = 'rgba(255, 255, 255, 1)';
        else if(c=='g') color = 'rgba(128, 128, 128, 1)';
        else if(c=='t') color = 'rgba(128, 0, 128, 1)';
        else if(c=='l') color = 'rgba(255, 128, 0, 1)';
        else if(c=='s') color = 'rgba(0, 255, 0, 1)';
        else if(c=='z') color = 'rgba(255, 0, 0, 1)';
        else if(c=='o') color = 'rgba(255, 255, 0, 1)';
        else if(c=='j') color = 'rgba(0, 0, 255, 1)';
        else if(c=='i') color = 'rgba(0, 255, 255, 1)';
        else color = 'rgba(0, 0, 0, 1)';
        ctx.fillStyle = color;
        ctx.fillRect(pos_x - off + x*sz, pos_y - off + y*sz, sz-pad, sz-pad);

        x += 1;  
        if(x==4) {
          x = 0;
          y += 1;
        }
      }
    }

    function getBezierXY(t, sx, sy, cp1x, cp1y, cp2x, cp2y, ex, ey) {
      return {
        x: Math.pow(1-t,3) * sx + 3 * t * Math.pow(1 - t, 2) * cp1x 
          + 3 * t * t * (1 - t) * cp2x + t * t * t * ex,
        y: Math.pow(1-t,3) * sy + 3 * t * Math.pow(1 - t, 2) * cp1y 
          + 3 * t * t * (1 - t) * cp2y + t * t * t * ey
      };
    }

    function getQuadraticXY(t, sx, sy, cp1x, cp1y, ex, ey) {
      return {
        x: (1-t) * (1-t) * sx + 2 * (1-t) * t * cp1x + t * t * ex,
        y: (1-t) * (1-t) * sy + 2 * (1-t) * t * cp1y + t * t * ey
      };
    }

    const Graph = ForceGraph()
      (document.getElementById('graph'))
        .linkColor(() => 'rgba(255,255,255,0.5)')
        .linkDirectionalParticles(2)
//        .linkCurvature('curvature')
        .nodeCanvasObject((node, ctx, globalScale) => drawTetrisField(node.id, node.x, node.y, ctx))
        .d3Force('collide', d3.forceCollide(30).strength(1) )
//        .d3Force('link', d3.forceLink().distance(200).strength(-100) )
        .d3Force('charge', d3.forceManyBody().strength(-30 ))
//        .d3Force('radial', d3.forceRadial().radius((node) => { return node.id.search(/[sztljoi]/) == -1 ? 500 : 0 }).strength(1) );
        .graphData(gData);
  </script>

