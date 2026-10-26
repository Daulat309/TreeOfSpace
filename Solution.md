```java
package Juspay;

import java.util.*;

class TreeNode {
    String name;
    int lockedBy;
    boolean isLocked;
    List<TreeNode> childs;
    TreeNode parent;
    Set<TreeNode> lockedDescendents;

    // Constructor
    TreeNode(String name, TreeNode parent) {
        this.name = name;
        this.lockedBy = -1;
        this.parent = parent;
        this.isLocked = false;
        this.childs = new ArrayList<>();
        this.lockedDescendents = new HashSet<>();
    }

    // Create children of the current node
    void addChild(List<String> names) {
        for (String nm : names) {
            childs.add(new TreeNode(nm, this));
        }
    }
}

class MAryTree {
    TreeNode root;
    Map<String, TreeNode> nameToTreeNodeMapping;

    // Constructor
    MAryTree(String name) {
        root = new TreeNode(name, null);
        nameToTreeNodeMapping = new HashMap<>();
    }

    // Build the M-ary tree using BFS
    void makeMArtTree(List<String> a, int m) {
        Queue<TreeNode> q = new LinkedList<>();

        int k = 1;
        int n = a.size();

        q.offer(root);

        while (!q.isEmpty()) {
            TreeNode r = q.poll();

            nameToTreeNodeMapping.put(r.name, r);

            List<String> b = new ArrayList<>();

            int i;
            for (i = k; i < Math.min(n, k + m); i++) {
                b.add(a.get(i));
            }

            r.addChild(b);

            for (TreeNode child : r.childs) {
                q.offer(child);
            }

            k = i;
        }
    }

    // Print the tree for debugging
    void print(TreeNode r) {
        if (r == null) {
            return;
        }

        System.out.println("TreeNode -> " + r.name + " " + r.lockedBy);
        System.out.println("Childs -> ");

        for (TreeNode child : r.childs) {
            System.out.println("       " + child.name);
        }

        System.out.println("Locked -> ");

        for (TreeNode child : r.lockedDescendents) {
            System.out.println("       " + child.name);
        }

        for (TreeNode child : r.childs) {
            print(child);
        }
    }

    // Update all ancestors after locking a node
    void updateParents(TreeNode r, TreeNode curr) {
        while (r != null) {
            r.lockedDescendents.add(curr);
            r = r.parent;
        }
    }

    // Lock a node
    boolean lock(String name, int id) {
        TreeNode r = nameToTreeNodeMapping.get(name);

        if (r.isLocked || !r.lockedDescendents.isEmpty()) {
            return false;
        }

        TreeNode par = r.parent;

        // Check whether any ancestor is locked
        while (par != null) {
            if (par.isLocked) {
                return false;
            }
            par = par.parent;
        }

        updateParents(r.parent, r);

        r.isLocked = true;
        r.lockedBy = id;

        return true;
    }

    // Unlock a node
    boolean unlock(String name, int id) {
        TreeNode r = nameToTreeNodeMapping.get(name);

        if (!r.isLocked || r.lockedBy != id) {
            return false;
        }

        TreeNode par = r.parent;

        // Remove this node from all ancestors' locked descendants
        while (par != null) {
            par.lockedDescendents.remove(r);
            par = par.parent;
        }

        r.isLocked = false;
        r.lockedBy = -1;

        return true;
    }

    // Upgrade a node's lock
    boolean upgradeLock(String name, int id) {
        TreeNode r = nameToTreeNodeMapping.get(name);

        if (r.isLocked || r.lockedDescendents.isEmpty()) {
            return false;
        }

        // All locked descendants must belong to the same user
        for (TreeNode ld : r.lockedDescendents) {
            if (ld.lockedBy != id) {
                return false;
            }
        }

        // Check whether any ancestor is locked
        TreeNode par = r.parent;

        while (par != null) {
            if (par.isLocked) {
                return false;
            }
            par = par.parent;
        }

        // Create a copy because unlock() modifies lockedDescendents
        Set<TreeNode> stt = new HashSet<>(r.lockedDescendents);

        // Unlock all locked descendants
        for (TreeNode ld : stt) {
            unlock(ld.name, id);
        }

        // Lock the current node
        lock(name, id);

        return true;
    }
}

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        int m = sc.nextInt();
        int t = sc.nextInt();

        List<String> a = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            a.add(sc.next());
        }

        MAryTree tree = new MAryTree(a.get(0));
        tree.makeMArtTree(a, m);

        while (t-- > 0) {
            int opType = sc.nextInt();
            String name = sc.next();
            int id = sc.nextInt();

            if (opType == 1) {
                System.out.println(tree.lock(name, id) ? "true" : "false");
            } else if (opType == 2) {
                System.out.println(tree.unlock(name, id) ? "true" : "false");
            } else if (opType == 3) {
                System.out.println(tree.upgradeLock(name, id) ? "true" : "false");
            }
        }

        sc.close();
    }
}
```
