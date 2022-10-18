# MACsec using Pre-Shared-Key

This repo contains the YANG RPC files needed to create a MACsec network link using Pre-Shared-Key as the Authentication Method. You can send these RPCs to the Catalyst 9000 switch using the tool of your choice.

These RPCs are very basic and will not meet all needs, however applying these to two back to back Catalyst 9000 switches should get a MACsec link up and encrypting traffic.  You can enable specific options & features by using Cisco YANG Suite to create new RPCs that suit your own specific needs.  There are comments in each of these RPC files, please review each of these RPCs in a text editor.


## 1. Key Chain Config
This RPC file will create the Key Chain. The key chain contains the CKN and the CAK. This key chain will be applied to the interface in Step 3.

In this RPC:
* the name of the key chain is RESTCONF-KEY
* the CKN is 9999.  You can change this however the CKN needs to be an even number
* the CAK is 64 bits of 9's.   AES128 requires 32 bits and AES256 requires 64 bits. This needs to be in hex characters. 

![]


## 2. MKA\ MACsec Key Agreement Config
This RPC file will create the MKA Policy.

In this RPC:
* the name of the MKA POLICY is MKAPOLICY-G1-0-4. I like to name my MKA polices based on the interface they will be attached to, please use your own MKA policy name. 
* the MACsec crypto algorithm is 256 bits, this is supported by Catalyst 9300. Use 128 bits for Catalyst 9200. 
* the SAK Rekey is configured to hit at 600 seconds. This is not mandatory and can be configured according to your requirements. 


## 3. MACsec Interface Config
Please be sure to review the comments in this RPC and assure you are applying this config to the proper interface. 

This RPC file will:
* configure the interface to use the configured key chain
* configure the interface to use the configured MKA policy
* configure MACsec network link 


