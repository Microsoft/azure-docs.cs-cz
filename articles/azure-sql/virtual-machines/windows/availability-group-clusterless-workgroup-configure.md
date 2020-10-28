---
title: Konfigurace skupiny dostupnosti pracovní skupiny nezávislé na doméně
description: Přečtěte si, jak nakonfigurovat skupinu dostupnosti Always On v doméně nezávislou na doméně služby Active Directory ve virtuálním počítači s SQL Server v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 6bfea42c6fca3369485ccf7a47158f7420df9c9c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790028"
---
# <a name="configure-a-workgroup-availability-group"></a>Konfigurace skupiny dostupnosti pracovní skupiny 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje kroky potřebné k vytvoření clusteru nezávislého na doméně služby Active Directory se skupinou dostupnosti Always On. Tato funkce se také označuje jako cluster pracovní skupiny. Tento článek se zaměřuje na kroky, které jsou důležité pro přípravu a konfiguraci pracovní skupiny a skupiny dostupnosti, a lesky na kroky, které jsou pokryté v jiných článcích, jako je vytvoření clusteru nebo nasazení skupiny dostupnosti. 


## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci skupiny dostupnosti pracovních skupin budete potřebovat následující:
- Alespoň dva virtuální počítače se systémem Windows Server 2016 (nebo vyšší), které používají SQL Server 2016 (nebo vyšší), nasazené do stejné skupiny dostupnosti nebo jiné zóny dostupnosti pomocí statických IP adres. 
- Místní síť s minimálně 4 bezplatnými IP adresami v podsíti. 
- Účet na každém počítači ve skupině správce, který má také práva sysadmin v rámci SQL Server. 
- Otevřete porty: TCP 1433, TCP 5022, TCP 59999. 

Pro referenci se v tomto článku používají následující parametry, které je ale možné upravit podle potřeby: 

| **Název** | **Parametr** |
| :------ | :---------------------------------- |
| **Uzel1**   | AGNode1 (10.0.0.4) |
| **Uzel2**   | AGNode2 (10.0.0.5) |
| **Název clusteru** | AGWGAG (10.0.0.6) |
| **Naslouchací proces** | AGListener (10.0.0.7) | 
| **Přípona DNS** | ag.wgcluster.example.com | 
| **Název pracovní skupiny** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Nastavit příponu DNS 

V tomto kroku nakonfigurujte pro oba servery příponu DNS. Například, `ag.wgcluster.example.com`. To vám umožní použít název objektu, ke kterému se chcete připojit, jako plně kvalifikovanou adresu v rámci vaší sítě, například `AGNode1.ag.wgcluster.example.com` . 

K nakonfigurování přípony DNS použijte následující postup:

1. Protokol RDP do prvního uzlu a otevřete Správce serveru. 
1. Vyberte **místní server** a pak v části **název počítače** vyberte název vašeho virtuálního počítače. 
1. V části **Chcete-li přejmenovat tento počítač** , vyberte možnost **změnit.** ... 
1. Název pracovní skupiny změňte na něco smysluplného, například `AGWORKGROUP` : 

   ![Změnit název pracovní skupiny](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Pokud chcete otevřít dialogové okno **přípona DNS a název počítače pro rozhraní NetBIOS** , vyberte **Další...** 
1. Do pole **Přípona primárního serveru DNS tohoto počítače** zadejte název vaší přípony DNS, například `ag.wgcluster.example.com` a pak vyberte **OK** : 

   ![Přidat příponu DNS](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Potvrďte, že **úplný název počítače** teď zobrazuje příponu DNS, a pak vyberte **OK** , aby se změny uložily: 

   ![Přidat příponu DNS](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Až se vám zobrazí výzva, restartujte server. 
1. Opakujte tyto kroky na všech ostatních uzlech, které chcete použít pro skupinu dostupnosti. 

## <a name="edit-a-host-file"></a>Upravit soubor hostitele

Vzhledem k tomu, že neexistuje žádná služba Active Directory, neexistuje způsob, jak ověřit připojení systému Windows. V takovém případě přiřaďte důvěryhodnost úpravou souboru hostitele pomocí textového editoru. 

Chcete-li upravit soubor hostitele, postupujte podle následujících kroků:

1. Protokol RDP ve vašem virtuálním počítači. 
1. Pomocí **Průzkumníka souborů** přejdete na `c:\windows\system32\drivers\etc` . 
1. Klikněte pravým tlačítkem myši na soubor **hosts** a otevřete soubor pomocí **poznámkového bloku** (nebo jakéhokoli jiného textového editoru).
1. Na konci souboru přidejte položku pro každý uzel, skupinu dostupnosti a naslouchací proces ve formě `IP Address, DNS Suffix #comment` jako: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Přidat položky pro IP adresu, cluster a naslouchací proces do souboru hostitele](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Nastavení oprávnění

Vzhledem k tomu, že není k dispozici žádná služba Active Directory pro správu oprávnění, je nutné ručně připustit účet místního správce, který není typu Builtin, a vytvořit cluster. 

Uděláte to tak, že v relaci PowerShellu pro správu na všech uzlech spustíte následující rutinu PowerShellu: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání

V tomto kroku vytvoříte cluster s podporou převzetí služeb při selhání. Pokud tyto kroky neznáte, můžete je sledovat v [kurzu cluster s podporou převzetí služeb při selhání](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

Významné rozdíly mezi kurzem a to, co je potřeba udělat pro cluster pracovních skupin:
- Zrušte kontrolu **úložiště** a při spuštění ověření clusteru **prostory úložiště s přímým přístupem** . 
- Při přidávání uzlů do clusteru přidejte plně kvalifikovaný název, například:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Zrušte kontrolu **Přidat do clusteru veškeré oprávněné úložiště** . 

Po vytvoření clusteru přiřaďte IP adresu statického clusteru. To můžete provést pomocí těchto kroků:

1. Na jednom z uzlů otevřete **Správce clusteru s podporou převzetí služeb při selhání** , vyberte cluster, klikněte pravým tlačítkem myši na **Název: \<ClusterNam>** v části **základní prostředky clusteru** a pak vyberte **vlastnosti** . 

   ![Spustit vlastnosti pro název clusteru](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. V části **IP adresy** vyberte IP adresu a pak vyberte **Upravit** . 
1. Vyberte **použít statické** , zadejte IP adresu clusteru a pak vyberte **OK** : 

   ![Zadejte statickou IP adresu clusteru.](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Ověřte, zda nastavení vypadá správně, a pak vyberte **OK** a uložte je:

   ![Ověřit vlastnosti clusteru](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Vytvoření určujícího cloudu 

V tomto kroku nakonfigurujte určující sdílenou složku v cloudu. Pokud nejste obeznámeni s postupem, přečtěte si téma [nasazení cloudového určujícího clusteru pro cluster s podporou převzetí služeb při selhání](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>Povolit funkci Skupina dostupnosti 

V tomto kroku povolíte funkci Skupina dostupnosti. Pokud nejste obeznámeni s postupem, Projděte si [kurz skupiny dostupnosti](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Vytváření klíčů a certifikátů

V tomto kroku vytvoříte certifikáty, které používá přihlášení SQL na šifrovaném koncovém bodu. V každém uzlu vytvořte složku, do které se budou ukládat zálohy certifikátů, například `c:\certs` . 

Chcete-li nakonfigurovat první uzel, postupujte podle následujících kroků: 

1. Otevřete **SQL Server Management Studio** a připojte se k prvnímu uzlu, například `AGNode1` . 
1. Otevřete **nové okno dotazu** a po aktualizaci na složité a zabezpečené heslo spusťte následující příkaz Transact-SQL (T-SQL):

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

1. Dále vytvořte koncový bod HADR a použijte certifikát pro ověřování spuštěním tohoto příkazu jazyka Transact-SQL (T-SQL):

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

1. Pomocí **Průzkumníka souborů** přejdete do umístění souboru, ve kterém je váš certifikát, například `c:\certs` . 
1. Ručně vytvořte kopii certifikátu, například `AGNode1Cert.crt` , z prvního uzlu a přeneste ho do stejného umístění na druhém uzlu. 

Pokud chcete nakonfigurovat druhý uzel, postupujte podle těchto kroků: 

1. Připojte se k druhému uzlu pomocí **SQL Server Management Studio** , například `AGNode2` . 
1. V **novém okně dotazu** spusťte následující příkaz Transact-SQL (T-SQL) po aktualizaci na složité a zabezpečené heslo: 

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

1. Dále vytvořte koncový bod HADR a použijte certifikát pro ověřování spuštěním tohoto příkazu jazyka Transact-SQL (T-SQL):

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

1. Pomocí **Průzkumníka souborů** přejdete do umístění souboru, ve kterém je váš certifikát, například `c:\certs` . 
1. Ručně vytvořte kopii certifikátu, například `AGNode2Cert.crt` , z druhého uzlu a přeneste ho do stejného umístění na prvním uzlu. 

Pokud v clusteru existují nějaké jiné uzly, opakujte tyto kroky a změňte také příslušné názvy certifikátů. 

## <a name="create-logins"></a>Vytváření přihlašovacích údajů

Ověřování certifikátů se používá k synchronizaci dat mezi uzly. Pokud to chcete povolit, vytvořte přihlášení k druhému uzlu, vytvořte uživatele pro přihlášení, vytvořte certifikát pro přihlášení k použití zálohovaného certifikátu a pak udělte připojení ke koncovému bodu zrcadlení. 

Provedete to tak, že nejprve spustíte následující dotaz Transact-SQL (T-SQL) na prvním uzlu, například `AGNode1` : 

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

Dále spusťte následující dotaz Transact-SQL (T-SQL) na druhém uzlu, například `AGNode2` : 

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

Pokud v clusteru existují nějaké jiné uzly, opakujte tyto kroky a změňte také příslušné certifikáty a uživatelská jména. 

## <a name="configure-an-availability-group"></a>Konfigurace skupiny dostupnosti

V tomto kroku Nakonfigurujte skupinu dostupnosti a přidejte do ní své databáze. V tuto chvíli nevytvářejte naslouchací proces. Pokud nejste obeznámeni s postupem, Projděte si [kurz skupiny dostupnosti](availability-group-manually-configure-tutorial.md#create-the-availability-group). Nezapomeňte zahájit převzetí služeb při selhání a navrácení služeb po obnovení, abyste ověřili, že vše funguje tak, jak by mělo. 

   > [!NOTE]
   > Pokud během procesu synchronizace dojde k chybě, může být nutné udělit `NT AUTHORITY\SYSTEM` oprávnění sysadmin k vytváření prostředků clusteru v prvním uzlu, například `AGNode1` dočasně. 

## <a name="configure-a-load-balancer"></a>Konfigurace nástroje pro vyrovnávání zatížení

V tomto posledním kroku nakonfigurujte nástroj pro vyrovnávání zatížení pomocí [Azure Portal](availability-group-load-balancer-portal-configure.md) nebo [PowerShellu](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Další kroky

Ke konfiguraci skupiny dostupnosti můžete použít taky [příkaz AZ SQL VM CLI](./availability-group-az-commandline-configure.md) .