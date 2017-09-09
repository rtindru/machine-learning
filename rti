import Queue

class RTIGraph():
    def __init__(self, edges, directed=True):
        self.edges = edges
        self.directed = directed
        self.G = {}
        self._form_graph(directed)
    
    def _form_graph(self, directed):
        for v1, v2 in self.edges:
            self.make_link(v1, v2, directed)
   
    def make_link(self, node1, node2, directed):
        if node1 not in self.G:
            self.G[node1] = {}
        (self.G[node1])[node2] = 1
        if not directed:
            if node2 not in self.G:
                self.G[node2] = {}
            (self.G[node2])[node1] = 1
        return self.G

    def check_connection(self, node1, node2):
        # Return True if node1 is connected to node2 in G
        # or False if otherwise
        marked = {}
        self.mark_component(node1, marked)
        return node2 in marked
    
    def mark_component(self, node, marked):
        marked[node] = True
        total_marked = 1
        for neighbor in self.G.get(node, []):
            if neighbor not in marked:
                total_marked += mark_component(self.G, neighbor, marked)
        return total_marked
    
    def dft(self, node):
        open_list = Queue.LifoQueue()
        open_list.put(node)
        return self._traverse(node, open_list, mode='traverse')
     
    def bft(self, node):
        open_list = Queue.Queue()
        open_list.put(node)
        return self._traverse(node, open_list, mode='traverse')
    
    def dfs(self, node, search):
        open_list = Queue.LifoQueue()
        open_list.put(node)
        return self._traverse(node, open_list, mode='search', search=search)
    
    def bfs(self, node, search):
        open_list = Queue.Queue()
        open_list.put(node)
        return self._traverse(node, open_list, mode='search', search=search)
    
    def sssp(self, node):
        open_list = Queue.Queue()
        open_list.put(node)
        return self._traverse(node, open_list, mode='all-paths')
    
    def sp(self, node1, node2):
        open_list = Queue.Queue()
        open_list.put(node1)
        return self._traverse(node1, open_list, mode='path', search=node2)
    
    def _traverse(self, node, open_list, mode='traverse', search=None):
        if mode == 'search' and search is None:
            raise ValidationError('Search parameter must be supplied')
        
        marked = {}
        marked[node] = [node]
        return_list = []

        while not open_list.empty():
            node = open_list.get()

            if mode == 'search':
                if node == search:
                    return True
            elif mode == 'traverse':
                return_list.append(node)
            elif mode == 'path':
                pass

            for neighbour in self.G.get(node, []):
                if neighbour in marked:
                    continue
                else:
                    marked[neighbour] = marked[node] + [neighbour]
                    if neighbour == search and mode == 'path':
                        return marked[neighbour]
                    open_list.put(neighbour)
        if mode == 'traverse':
            return return_list
        elif mode == 'search':
            return False
        elif mode == 'path':
            return marked.get(search, False)
        elif mode == 'all-paths':
            return marked
