<!-- .slide: data-state="section-break" id="terminology" -->
# Brief interlude: `nova evacuate`


<!-- .slide: data-state="normal" id="nova-evacuate" -->
## `nova evacuate`

*   API provided by `nova` for initiating recovery of VM
*   http://docs.openstack.org/admin-guide/cli_nova_evacuate.html

```sh
# nova help evacuate
usage: nova evacuate [--password <password>] [--on-shared-storage]
                     <server> [<host>]

Evacuate server from failed host.

# nova help host-evacuate
usage: nova host-evacuate [--target_host <target_host>] [--on-shared-storage]
                          <host>

Evacuate all instances from failed host.
```

*   Used by most HA solutions
*   Without shared storage, simply rebuilds from scratch


<!-- .slide: data-state="normal" id="public-health-warning" -->
## Public Health Warning

### `nova evacuate` does not really mean evacuation!

<img alt="skull and cross-bones warning triangle"
     src="images/hazardous.gif" />


<!-- .slide: data-state="normal" id="earthquakes" class="earthquake" -->
## Think about earthquakes

<div class="row vcenter before">
    <div class="col-md-6">
        <img src="images/earthquake-before.jpg" alt="church before an earthquake"
             class="pull-right" />
    </div>
    <div class="text col-md-6">
        <h3 class="fragment" data-fragment-index="1">
            Not too late to evacuate
        </h3>
    </div>
</div>
<br clear="left" />
<div class="row vcenter after">
    <div class="col-md-6">
        <img src="images/earthquake-after.jpg" alt="church after an earthquake"
             class="pull-right" />
    </div>
    <div class="text col-md-6">
        <h3 class="fragment" data-fragment-index="1">
            Too late to evacuate
        </h3>
    </div>
</div>


<!-- .slide: data-state="normal" id="nova-terminology" class="earthquake" -->
## `nova` terminology

<div class="row vcenter before">
    <div class="col-md-6">
        <img src="images/earthquake-before.jpg" alt="church before an earthquake"
             class="pull-right" />
    </div>
    <div class="text col-md-6">
        <h3>
            `nova live-migration`
        </h3>
    </div>
</div>
<br clear="left" />
<div class="row vcenter after">
    <div class="col-md-6">
        <img src="images/earthquake-after.jpg" alt="church after an earthquake"
             class="pull-right" />
    </div>
    <div class="text col-md-6">
        <h3>
            `nova evacuate` ?!
        </h3>
    </div>
</div>

<!-- .slide: data-state="normal" id="health-warning-summary" -->
# Public Health Warning

*   `nova evacuate` does *not* do evacuation
*   `nova evacuate` does resurrection (after releasing dependencies)
*   In Vancouver, `nova` developers considered a rename
    *    Has not happened yet
    *    Due to impact, seems unlikely to happen any time soon

<h2 class="fg-dark-green" style="margin-top: 50px;">
    Whenever you see “*evacuate*” in a `nova`-related context,
    pretend you saw “*resurrect*”
</h2>

<!-- .slide: data-state="normal" id="ocf" data-menu-title="OCF RAs" -->
## `NovaCompute` / `NovaEvacuate` OCF agents

*   Custom OCF Resource Agents (RAs)
    *   Pacemaker plugins to manage resources
*   Used by Red Hat / SUSE; contributions by Intel also
*   Custom fencing agent (`fence_compute`) flags host for recovery
*   `NovaEvacuate` RA polls for flags, and initiates recovery
    *   Will keep retrying if recovery not possible
*   `NovaCompute` RA starts / stops `nova-compute`
    *   Start waits for recovery to complete
*   RAs
    [upstream in `openstack-resource-agents` repo](https://github.com/openstack/openstack-resource-agents/tree/master/ocf)
    (maintained by me)


<!-- .slide: data-state="normal" id="pacemaker_remote" data-menu-title="pacemaker_remote" -->
## `NovaCompute` / `NovaEvacuate` OCF agents

Scalability issue solved by `pacemaker_remote`

*   New(-ish) Pacemaker feature
*   Allows core cluster nodes to control "remote"
    nodes via a `pacemaker_remote` proxy service (daemon)
*   Can scale to very large numbers


<!-- .slide: data-state="normal" id="ocf-architecture" data-menu-title="architecture" -->
## `NovaCompute` / `NovaEvacuate` OCF agents

<img alt="Architecture with pacemaker_remote" class="full-slide"
     src="images/pacemaker_remote.svg" />

