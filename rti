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
   
    def make_link(self, node1, node2, directed, graph=None):
        if graph is None:
            graph = self.G
        if node1 not in graph:
            graph[node1] = {}
        graph[node1][node2] = 1
        if not directed:
            if node2 not in graph:
                graph[node2] = {}
            graph[node2][node1] = 1
        return graph

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
    
    def build_strong_weak_tree(self, node, strong=None, weak=None):
        seen = set(node)
        strong, weak = {}, {}
        queue = Queue.Queue()
        queue.put(node)
        while not queue.empty():
            node = queue.get()
            for neighbour in self.G.get(node, []):
                if neighbour in seen:
                    if neighbour in strong:
                        pass
                    else:
                        self.make_link(node, neighbour, False, graph=weak)
                else:
                    self.make_link(node, neighbour, True, graph=strong)
                    seen.add(neighbour)
                    queue.put(neighbour)
        return strong, weak
        
    def get_post_order(self, tree, root, post, count=1):
        children = tree.get(root, None)
        if children is None:
            post[root] = count
            return count + 1
        try:
            c1, c2 = children.keys()
            cnt1 = self.get_post_order(tree, c1, post, count)
            cnt2 = self.get_post_order(tree, c2, post, cnt1)
            post[root] = cnt2
            return cnt2 + 1
        except:
            c1 = children.keys()[0]
            cnt1 = self.get_post_order(tree, c1, post, count)
            post[root] = cnt1
            return cnt1 + 1
        
    def compute_ND(self, tree, root, ND):
        children = tree.get(root, None)
        if children is None:
            ND[root] = 1
            return 

        for child in children.keys():
            self.compute_ND(tree, child, ND)

        total = 1
        for child in children.keys():    
            total += ND[child]
            
        ND[root] = total
    
    def compute_high_low(self, root, weak, strong, high, low, post, min_so_far=None, max_so_far=None):
        children = strong.get(root, None)
        if children is None:
            weaks = weak.get(root, None)
            if weaks:
                candidates = weaks.keys()
            else:
                candidates = []
            candidates.append(root)
            candidate_posts = [post[x] for x in candidates]
            if min_so_far:
                candidate_posts.append(min_so_far)
            if max_so_far:
                candidate_posts.append(max_so_far)
            mn, mx = min(candidate_posts), max(candidate_posts)
            high[root] = mx
            low[root] = mn
            return mn, mx

        for child in children.keys():
            min_so_far, max_so_far = self.compute_high_low(child, weak, strong, high, low, post, min_so_far, max_so_far)
        
        weaks = weak.get(root, None)
        if weaks:
            weak_refs = weaks.keys()
        else:
            weak_refs = []
        weak_posts = [post[x] for x in weak_refs]
        weak_posts.append(post[root])
        weak_posts.append(min_so_far)
        weak_posts.append(max_so_far)
        mn, mx = min(weak_posts), max(weak_posts)
        high[root] = mx
        low[root] = mn
        return mn, mx


    def find_bridges(self, node=None):
        """
        Naive implementation of the Takran Algorithm
        """
        if node is None:
            node = self.G.keys()[0]
        strong_links, weak_links = self.build_strong_weak_tree(node)
        post_order, ND, high, low = {}, {}, {}, {}
        self.get_post_order(strong_links, node, post_order, 1)
        self.compute_ND(strong_links, node, ND)
        self.compute_high_low(node, weak_links, strong_links, high, low, post_order)
        bridges = []
        for node, children in strong_links.items():
            for child in children:
                edge = (node, child)
                if high[child] <= post[child] and low[child] > (post[child] - ND[child]):
                    bridges.append(edge)
        return bridges

