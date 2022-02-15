This class provides the building blocks for someone wanting to use PHP to talk to Proxmox MG.
Relatively simple piece of code, just provides a get/put/post/delete abstraction layer as methods
on top of Proxmox's REST API, while also handling the Login Ticket headers required for authentication.

See https://pmg.proxmox.com/pmg-docs/pmg-admin-guide.html for information about how this API works.
API browser available at: https://pmg.proxmox.com/pmg-docs/api-viewer/index.html

WIP: Repo being adapted, tested & deployed for PMG, not PVE.

## Requirements: ##

PHP 7 with cURL (including SSL/TLS) support.

## Usage: ##

Example - Return status array for each Proxmox Host in this cluster.

    require("./PMG-API-PHP-Client/pmg_api.class.php");

    # You can try/catch exception handle the constructor here if you want.
    $pve2 = new PMG_API("hostname", "username", "realm", "password");
    # realm above can be pve, pam or any other realm available.

    /* Optional - enable debugging. It print()'s any results currently */
    // $pmg->set_debug(true);

    if ($pmg->login()) {
        foreach ($pmg->get_node_list() as $node_name) {
            print_r($pmg->get("/nodes/".$node_name."/status"));
        }
    } else {
        print("Login to Proxmox Host failed.\n");
        exit;
    }

Example - Create a new OpenVZ Container on the first host in the cluster.

    require("./pve2-api-php-client/pve2_api.class.php");

    # You can try/catch exception handle the constructor here if you want.
    $pve2 = new PVE2_API("hostname", "username", "realm", "password");
    # realm above can be pve, pam or any other realm available.

    /* Optional - enable debugging. It print()'s any results currently */
    // $pve2->set_debug(true);

    if ($pve2->login()) {

        # Get first node name.
        $nodes = $pve2->get_node_list();
        $first_node = $nodes[0];
        unset($nodes);

        # Create a VZ container on the first node in the cluster.
        $new_container_settings = array();
        $new_container_settings['ostemplate'] = "local:vztmpl/debian-6.0-standard_6.0-4_amd64.tar.gz";
        $new_container_settings['vmid'] = "1234";
        $new_container_settings['cpus'] = "2";
        $new_container_settings['description'] = "Test VM using Proxmox 2.0 API";
        $new_container_settings['disk'] = "8";
        $new_container_settings['hostname'] = "testapi.domain.tld";
        $new_container_settings['memory'] = "1024";
        $new_container_settings['nameserver'] = "4.2.2.1";

        // print_r($new_container_settings);
        print("---------------------------\n");

        print_r($pve2->post("/nodes/".$first_node."/openvz", $new_container_settings));
        print("\n\n");
    } else {
        print("Login to Proxmox Host failed.\n");
        exit;
    }

Example - Modify DNS settings on an existing container on the first host.

    require("./pve2-api-php-client/pve2_api.class.php");

    # You can try/catch exception handle the constructor here if you want.
    $pve2 = new PVE2_API("hostname", "username", "realm", "password");
    # realm above can be pve, pam or any other realm available.

    /* Optional - enable debugging. It print()'s any results currently */
    // $pve2->set_debug(true);

    if ($pve2->login()) {

        # Get first node name.
        $nodes = $pve2->get_node_list();
        $first_node = $nodes[0];
        unset($nodes);

        # Update container settings.
        $container_settings = array();
        $container_settings['nameserver'] = "4.2.2.2";

        # NOTE - replace XXXX with container ID.
        var_dump($pve2->put("/nodes/".$first_node."/openvz/XXXX/config", $container_settings));
    } else {
        print("Login to Proxmox Host failed.\n");
        exit;
    }

Example - Delete an existing container.

    require("./pve2-api-php-client/pve2_api.class.php");

    # You can try/catch exception handle the constructor here if you want.
    $pve2 = new PVE2_API("hostname", "username", "realm", "password");
    # realm above can be pve, pam or any other realm available.

    /* Optional - enable debugging. It print()'s any results currently */
    // $pve2->set_debug(true);

    if ($pve2->login()) {
        # NOTE - replace XXXX with node short name, and YYYY with container ID.
        var_dump($pve2->delete("/nodes/XXXX/openvz/YYYY"));
    } else {
        print("Login to Proxmox Host failed.\n");
        exit;
    }

Licensed under the MIT License.
See LICENSE file.
