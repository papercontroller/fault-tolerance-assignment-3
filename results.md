==================================================
ASSIGNMENT 3 — CLOUD & MICROSERVICES RESILIENCE
AITU Digital Student Services Platform
==================================================

==================================================
PART B — AVAILABILITY CALCULATION
==================================================
Auth (2 replicas)         : 0.999975 (99.9975%)
Course (2 replicas)       : 0.999975 (99.9975%)
Registration (2 replicas) : 0.999975 (99.9975%)
Database (2 nodes)        : 0.999991 (99.9991%)
--------------------------------------------------
End-to-End Availability   : 0.998417 (99.842%)
Target Availability (SLA) : 0.9990 (99.9%)
Meets 99.9% Target?       : false
Bottleneck Component      : API Gateway (99.90% single instance)
With 2x API Gateway       : 0.999415 (99.942%) -> Meets Target: true

==================================================
PART D — AUTOMATED FAILURE SIMULATION ENGINE
==================================================

[Experiment 1] Injecting failure: Killing 1 Registration replica (pod-1)...
 -> Kubelet probe failed (Detection: 2s). Active replicas remaining: 1/2
 -> Service traffic routed to registration-service-pod-2 (0 requests dropped)
 -> ReplicaSet scheduled & started replacement pod (Recovery: 8s). State: 2/2 Ready.

[Experiment 2] Injecting failure: Terminating ALL Registration replicas (pod-1, pod-2)...
 -> API Gateway connection reset (Detection: 1s). Active endpoints: 0/2
 -> User requests returning HTTP 503 Service Unavailable (Circuit Breaker OPEN)
 -> Containers restarted and passed /ready probe (Recovery: 15s). State: 2/2 Ready.

[Experiment 3] Injecting failure: Primary Database node offline...
 -> DB heartbeat timeout detected (Detection: 3s). Primary DB unreachable.
 -> Promoting Standby DB node to Primary and updating connection pool...
 -> Automated failover completed (Recovery: 12s). Writes resumed.

--- Summary Table (Part D) ---
Exp | Injected Failure                       | Detection  | Recovery   | Status    
----+----------------------------------------+------------+------------+-----------
1   | One Registration replica failure       | 2s         | 8s         | RECOVERED 
2   | All Registration replicas failing      | 1s         | 15s        | RECOVERED 
3   | Database unavailability (Primary node crash) | 3s         | 12s        | RECOVERED 
