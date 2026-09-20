Event ID,Category,Description,SOC & Operational Significance
4624,Authentication,Successful Logon,"Baselines standard access; identifies logon type (e.g., Type 3 Network, Type 10 Remote)."
4625,Authentication,Failed Logon,"Primary indicator for credential brute-forcing, password spraying, or misconfigured services."
4648,Authentication,Logon using explicit credentials,"Flags lateral movement techniques (e.g., runas utility usage)."
4720,Account Management,User Account Created,Tracks identity lifecycle and flags unauthorized user provisioning.
4724,Account Management,Password Reset Attempt,Audits administrative credential resets across directory accounts.
4726,Account Management,User Account Deleted,Audit trail for identity offboarding or unauthorized account removal.
4732,Group Management,Member Added to Security Group,"Critical for detecting privilege escalation into high-value groups (e.g., Domain Admins)."
