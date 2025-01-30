# Leetcode---2493
Divide Nodes Into the Maximum Number of Groups
// Code in java
import java.util.*;

public class Solution {
    public int magnificentSets(int n, int[][] edges) {
        // Graph representation using adjacency list
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i <= n; i++) {
            graph.add(new ArrayList<>());
        }
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
            graph.get(edge[1]).add(edge[0]);
        }

        // Array to track which component a node belongs to
        int[] component = new int[n + 1];
        Arrays.fill(component, -1);
        
        // Array to store the maximum depth of each component
        int[] maxDepth = new int[n + 1];
        int componentId = 0;
        boolean isValid = true;

        // Finding connected components and checking bipartiteness
        for (int i = 1; i <= n; i++) {
            if (component[i] == -1) {
                List<Integer> nodes = new ArrayList<>();
                if (!isBipartite(graph, i, component, componentId, nodes)) {
                    return -1; // If not bipartite, return -1
                }
                // Find the max depth of BFS for this component
                int depth = 0;
                for (int node : nodes) {
                    depth = Math.max(depth, bfsDepth(graph, node, n));
                }
                maxDepth[componentId] = depth;
                componentId++;
            }
        }

        // Sum the max depths of each connected component
        int maxGroups = 0;
        for (int i = 0; i < componentId; i++) {
            maxGroups += maxDepth[i];
        }
        return maxGroups;
    }

    // BFS to check bipartiteness and assign components
    private boolean isBipartite(List<List<Integer>> graph, int start, int[] component, int componentId, List<Integer> nodes) {
        Queue<Integer> queue = new LinkedList<>();
        queue.add(start);
        component[start] = 0;
        nodes.add(start);

        while (!queue.isEmpty()) {
            int node = queue.poll();
            for (int neighbor : graph.get(node)) {
                if (component[neighbor] == -1) {
                    component[neighbor] = 1 - component[node];
                    queue.add(neighbor);
                    nodes.add(neighbor);
                } else if (component[neighbor] == component[node]) {
                    return false; // If two adjacent nodes have the same color, not bipartite
                }
            }
        }
        return true;
    }

    // BFS to determine the max depth of the component
    private int bfsDepth(List<List<Integer>> graph, int start, int n) {
        Queue<Integer> queue = new LinkedList<>();
        boolean[] visited = new boolean[n + 1];
        queue.add(start);
        visited[start] = true;
        int depth = 0;

        while (!queue.isEmpty()) {
            int size = queue.size();
            depth++;
            for (int i = 0; i < size; i++) {
                int node = queue.poll();
                for (int neighbor : graph.get(node)) {
                    if (!visited[neighbor]) {
                        visited[neighbor] = true;
                        queue.add(neighbor);
                    }
                }
            }
        }
        return depth;
    }
}
