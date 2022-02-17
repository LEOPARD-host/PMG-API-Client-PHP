This class provides the building blocks for someone wanting to use PHP to talk to Proxmox's API.
Relatively simple piece of code, just provides a get/put/post/delete abstraction layer as methods
on top of Proxmox's REST API, while also handling the Login Ticket headers required for authentication.

See https://pmg.proxmox.com/pmg-docs/pmg-admin-guide.html for information about how this API works.
API browser available at: https://pmg.proxmox.com/pmg-docs/api-viewer/index.html

WIP: Repo being adapted, tested & deployed for PMG, not PVE.

## Requirements: ##

PHP 7 with cURL (including SSL/TLS) support.

## Usage: ##

Example - Return status array for each Proxmox Host in this cluster.

    require_once("./PMG-API-Client-PHP/pmg_api.class.php");

    # You can try/catch exception handle the constructor here if you want.
    $pmg = new PMG_API("hostname", "username", "realm", "password");
    # realm above can be pve, pam or any other realm available.

    if ($pmg->login()) {
        foreach ($pmg->get_node_list() as $node_name) {
            print_r($pmg->get("/nodes/".$node_name."/status"));
        }
    } else {
        print("Login to Proxmox Host failed.\n");
        exit;
    }

Licensed under the MIT License.
See LICENSE file.
