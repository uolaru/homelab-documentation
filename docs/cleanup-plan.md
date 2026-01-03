# Homelab Cleanup Plan

## Phase 1: Access Recovery (Week 1)
- [ ] Reset root passwords on all running containers
- [ ] Document actual IP addresses
- [ ] Test access to each service
- [ ] Create password manager entry for each

## Phase 2: Service Audit (Week 2)
- [ ] Verify which services are actually being used
- [ ] Remove test VMs (twitch, deJoaca) or document purpose
- [ ] Decide on DNS strategy (unbound vs pihole)
- [ ] Evaluate Ollama vs OpenWebUI setup

## Phase 3: Consolidation (Week 3)
- [ ] Remove unused services
- [ ] Restart critical infrastructure (backup server, DNS)
- [ ] Organize containers with proper naming
- [ ] Set up container templates for future deployments

## Phase 4: Automation (Week 4+)
- [ ] Create Ansible playbooks for container deployment
- [ ] Automate backup strategy
- [ ] Set up monitoring for all services
- [ ] Document disaster recovery procedures

## Questions to Answer
4. Keep both Ollama and OpenWebUI or consolidate?
