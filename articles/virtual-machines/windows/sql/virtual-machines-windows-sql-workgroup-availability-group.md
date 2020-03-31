---
title: Konfigurace skupiny dostupnosti pracovní skupiny nezávislé na doméně
description: Zjistěte, jak nakonfigurovat skupinu dostupnosti na virtuálním počítači SQL Serveru v Azure, která je nezávislá na službě Active Directory domain.In a active directory domain-independent workgroup Always On availability group on a SQL Server virtual machine.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122672"
---
# <a name="configure-a-workgroup-availability-group"></a>Konfigurace skupiny dostupnosti pracovní skupiny 

Tento článek vysvětluje kroky nezbytné k vytvoření clusteru nezávislého na doméně služby Active Directory se skupinou dostupnosti Always On. To se také označuje jako cluster pracovní skupiny. Tento článek se zaměřuje na kroky, které jsou relevantní pro přípravu a konfiguraci pracovní skupiny a skupiny dostupnosti a glosy přes kroky, které jsou zahrnuty v jiných článcích, například jak vytvořit clusternebo nasadit skupinu dostupnosti. 


## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat skupinu dostupnosti pracovní skupiny, potřebujete následující:
- Alespoň dva virtuální počítače se systémem Windows Server 2016 (nebo vyšší) se systémem SQL Server 2016 (nebo vyšší) jsou nasazeny do stejné skupiny dostupnosti nebo do různých zón dostupnosti pomocí statických IP adres. 
- Místní síť s minimálně 4 volnými ADRESAMI IP v podsíti. 
- Účet na každém počítači ve skupině správců, který má také práva sysadmin v rámci SQL Server. 
- Otevřené porty: TCP 1433, TCP 5022, TCP 59999. 

Pro referenci se v tomto článku používají následující parametry, ale lze je podle potřeby upravit: 

| **Název** | **Parametr** |
| :------ | :---------------------------------- |
| **Uzel1**   | AGNode1 (10.0.0.4) |
| **Uzel2**   | AGNode2 (10.0.0.5) |
| **Název clusteru** | AGWGAG (10.0.0.6) |
| **Naslouchací proces** | AGListener (10.0.0.7) | 
| **Přípona DNS** | ag.wgcluster.example.com | 
| **Název pracovní skupiny** | AGWorkskupina | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Nastavení přípony DNS 

V tomto kroku nakonfigurujte příponu DNS pro oba servery. Například, `ag.wgcluster.example.com`. To umožňuje použít název objektu, ke kterému se chcete připojit, jako plně `AGNode1.ag.wgcluster.example.com`kvalifikovanou adresu v síti, například . 

Chcete-li nakonfigurovat příponu DNS, postupujte takto:

1. RDP do prvního uzlu a otevřete Správce serveru. 
1. Vyberte **Místní server** a potom v části **Název počítače**vyberte název virtuálního počítače . 
1. Vyberte **Změnit...** v části **Přejmenovat tento počítač...**. 
1. Změňte název názvu pracovní skupiny tak, `AGWORKGROUP`aby byl smysluplný, například : 

   ![Změna názvu pracovní skupiny](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Výběrem **možnosti Další...** otevřete dialogové okno **Dns Suffix a NetBIOS Computer Name** . 
1. Do pole **Primární přípona DNS tohoto počítače**zadejte název `ag.wgcluster.example.com` přípony DNS , například a vyberte **OK**: 

   ![Přidat příponu DNS](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Zkontrolujte, zda se nyní zobrazuje přípona DNS Úplný název **počítače,** a pak výběrem možnosti **OK** uložte změny: 

   ![Přidat příponu DNS](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Po zobrazení výzvy restartujte server. 
1. Opakujte tyto kroky na všech ostatních uzlech, které mají být použity pro skupinu dostupnosti. 

## <a name="edit-host-file"></a>Upravit soubor hostitele

Vzhledem k tomu, že neexistuje žádný active directory, neexistuje žádný způsob, jak ověřit připojení systému Windows. V takovém případě přiřaďte důvěryhodnost úpravou hostitelského souboru pomocí textového editoru. 

Chcete-li upravit hostitelský soubor, postupujte takto:

1. RDP do virtuálního počítače. 
1. Pomocí **Průzkumníka** souborů `c:\windows\system32\drivers\etc`přejděte na aplikaci . 
1. Klikněte pravým tlačítkem myši na soubor **hosts** a otevřete soubor pomocí **poznámkového bloku** (nebo jiného textového editoru).
1. Na konci souboru přidejte položku pro každý uzel, skupinu dostupnosti `IP Address, DNS Suffix #comment` a naslouchací proces ve formě jako: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Přidání položek pro adresu IP, cluster a naslouchací proces do hostitelského souboru](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Nastavení oprávnění

Vzhledem k tomu, že ke správě oprávnění není k dispozici žádný systém Active Directory, je třeba ručně povolit vytvoření clusteru neintegrovanému účtu místního správce. 

Chcete-li tak učinit, spusťte následující rutinu prostředí PowerShell v relaci prostředí PowerShell pro správu na každém uzlu: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání

V tomto kroku vytvoříte cluster s podporou převzetí služeb při selhání. Pokud nejste obeznámeni s těmito kroky, můžete je sledovat z [kurzu clusteru s podporou převzetí služeb při selhání](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Významné rozdíly mezi kurzem a tím, co je třeba udělat pro cluster pracovní skupiny:
- Při spuštění ověření clusteru **odškrtnete možnost Úložiště**a **Prostory úložiště direct.** 
- Při přidávání uzlů do clusteru přidejte plně kvalifikovaný název, například:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Zrušit zaškrtnutí políčka **Přidat do clusteru všechna způsobilá úložiště**. 

Po vytvoření clusteru přiřaďte statickou adresu IP clusteru. Postup je následující:

1. Na jednom z uzlů otevřete **Správce clusteru s podporou převzetí služeb při selhání**, vyberte cluster, klikněte pravým tlačítkem myši na příkaz **Název: \<ClusterNam>** v části Základní prostředky **clusteru** a vyberte **vlastnosti**. 

   ![Spuštění vlastností názvu clusteru](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Vyberte adresu IP v části **IP adresy** a vyberte **Upravit**. 
1. Vyberte **Použít statickou**adresu , zadejte ADRESU IP clusteru a pak vyberte **OK**: 

   ![Zadejte statickou adresu IP pro cluster.](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Ověřte, zda nastavení vypadá správně, a pak je chcete uložit výběrem **možnosti OK:**

   ![Ověření vlastností clusteru](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Vytvoření cloudového svědka 

V tomto kroku nakonfigurujte cloud share důkaz. Pokud nejste obeznámeni s kroky, podívejte se na [kurz clusteru s podporou převzetí služeb při selhání](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Povolit funkci skupiny dostupnosti 

V tomto kroku povolte funkci skupiny dostupnosti. Pokud nejste obeznámeni s kroky, podívejte se na [skupinu dostupnosti kurzu](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Vytvoření klíčů a certifikátu

V tomto kroku vytvořte certifikáty, které používá přihlášení SQL na šifrovaném koncovém bodu. Vytvořte složku na každém uzlu pro uložení záloh `c:\certs`certifikátů, například . 

Chcete-li nakonfigurovat první uzel, postupujte takto: 

1. Otevřete **sql server Management Studio** a připojte `AGNode1`se k prvnímu uzlu, například . 
1. Otevřete okno **Nový dotaz** a po aktualizaci na komplexní a zabezpečené heslo spusťte následující příkaz Transact-SQL (T-SQL):

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Dále vytvořte koncový bod HADR a použijte certifikát pro ověřování spuštěním tohoto příkazu Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Pomocí **Průzkumníka souborů** přejděte do umístění souboru, kde je certifikát, například `c:\certs`. 
1. Ručně vytvořit kopii certifikátu, `AGNode1Cert.crt`například , z prvního uzlu a přenést do stejného umístění na druhém uzlu. 

Chcete-li nakonfigurovat druhý uzel, postupujte takto: 

1. Připojte se k druhému uzlu pomocí aplikace `AGNode2`SQL Server Management **Studio**, například . 
1. V okně **Nový dotaz** spusťte po aktualizaci na komplexní a zabezpečené heslo následující příkaz Transact-SQL (T-SQL): 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Dále vytvořte koncový bod HADR a použijte certifikát pro ověřování spuštěním tohoto příkazu Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Pomocí **Průzkumníka souborů** přejděte do umístění souboru, kde je certifikát, například `c:\certs`. 
1. Ručně vytvořit kopii certifikátu, `AGNode2Cert.crt`například , z druhého uzlu a přenést do stejného umístění na prvním uzlu. 

Pokud jsou v clusteru jiné uzly, opakujte tyto kroky také a upravte názvy příslušných certifikátů. 

## <a name="create-logins"></a>Vytvořit přihlášení

Ověřování certifikátů se používá k synchronizaci dat mezi uzly. Chcete-li to povolit, vytvořte přihlášení pro druhý uzel, vytvořte uživatele pro přihlášení, vytvořte certifikát pro přihlášení k použití zálohovaného certifikátu a pak udělte připojení na zrcadlení koncového bodu. 

Chcete-li tak učinit, nejprve spusťte následující dotaz Transact-SQL (T-SQL) na prvním uzlu, například `AGNode1`: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Dále spusťte následující dotaz Transact-SQL (T-SQL) na `AGNode2`druhém uzlu, například : 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Pokud jsou v clusteru jiné uzly, opakujte tyto kroky také a upravte příslušný certifikát a uživatelská jména. 

## <a name="configure-availability-group"></a>Konfigurace skupiny dostupnosti

V tomto kroku nakonfigurujte skupinu dostupnosti a přidejte do ní databáze. V tuto chvíli nevytvářejte naslouchací proces. Pokud nejste obeznámeni s kroky, podívejte se na [skupinu dostupnosti kurzu](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Nezapomeňte zahájit převzetí služeb při selhání a navrácení služeb po selhání, abyste ověřili, že vše funguje tak, jak má být. 

   > [!NOTE]
   > Pokud dojde k selhání během procesu synchronizace, `NT AUTHORITY\SYSTEM` bude pravděpodobně nutné udělit práva sysadmin `AGNode1` k vytvoření prostředků clusteru na prvním uzlu, například dočasně. 

## <a name="configure-load-balancer"></a>Konfigurace pro vyrovnávání zatížení

V tomto posledním kroku nakonfigurujte správce zatížení pomocí [portálu Azure nebo](virtual-machines-windows-portal-sql-alwayson-int-listener.md) [Prostředí PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Další kroky

K konfiguraci skupiny dostupnosti můžete také použít [rozhraní příkazového příkazu Az SQL VM.](virtual-machines-windows-sql-availability-group-cli.md) 


