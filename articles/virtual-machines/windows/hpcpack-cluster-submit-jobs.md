---
title: Odeslání úloh HPC Pack clusteru v Azure | Dokumentace Microsoftu
description: Zjistěte, jak nastavit v místním počítači odesílat úlohy do clusteru HPC Pack v Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: ce8e2457c1d575e890174de3b9cf7faf6e16a7cb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258812"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Odeslání úloh HPC z místního počítače do clusteru HPC Pack nasazeného v Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Konfigurace v místním klientském počítači odesílat úlohy do [sady Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) clusteru v Azure. Tento článek ukazuje nastavení místního počítače s klientskými nástroji se odeslat úlohu pomocí protokolu HTTPS do clusteru v Azure. Tímto způsobem můžete několik uživatelů odesílat úlohy do clusteru HPC Pack založené na cloudu, ale bez přímé připojení k hlavnímu uzlu virtuálního počítače nebo přístupem k předplatnému Azure.

![Odeslat úlohu do clusteru v Azure][jobsubmit]

## <a name="prerequisites"></a>Požadavky
* **Hlavní uzel HPC Pack nasazený ve Virtuálním počítači Azure** – doporučujeme pomocí automatizovaných nástrojů, jako [šablona rychlého startu Azure](https://azure.microsoft.com/documentation/templates/) nasazení hlavního uzlu a clusteru. Budete potřebovat název DNS hlavního uzlu a přihlašovací údaje Správce clusteru k dokončení kroků v tomto článku.
* **Klientský počítač** -potřebujete klientského počítače Windows nebo Windows Server, který můžete spustit sady HPC Pack klienta nástroje (viz [požadavky na systém](https://technet.microsoft.com/library/dn535781.aspx)). Pokud chcete odesílat úlohy pomocí sady HPC Pack webového portálu nebo rozhraní REST API, můžete použít libovolného klientského počítače podle vašeho výběru.
* **Instalační médium sady HPC Pack** – Pokud chcete nainstalovat klientské nástroje sady HPC Pack bezplatné instalační balíček je k dispozici v nejnovější verzi sady HPC Pack [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Ujistěte se, že stahujete stejnou verzi sady HPC Pack, který je nainstalován hlavního uzlu virtuálního počítače.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Krok 1: Instalace a konfigurace webové komponenty hlavního uzlu
Povolit rozhraní REST odesílat úlohy do clusteru pomocí protokolu HTTPS, ujistěte se, že jsou webové součásti sady HPC Pack nakonfigurovány na hlavní uzel HPC Pack. Pokud požadavky nejsou nainstalovány, nejprve nainstalujte komponenty webové spuštěním HpcWebComponents.msi instalační soubor. Nakonfigurujte komponenty spuštěním skriptu prostředí HPC PowerShell **Set-HPCWebComponents.ps1**.

Podrobné pokyny najdete v části [nainstalovat webové komponenty Microsoft HPC Pack](https://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Některé šablony rychlý start Azure clusterů HPC Pack instalace a konfigurace webové komponenty automaticky.
> 
> 

**Chcete-li nainstalovat webové součásti**

1. Připojení k hlavnímu uzlu virtuálního počítače pomocí přihlašovacích údajů Správce clusteru.
1. Ve složce instalace sady HPC Pack spustit HpcWebComponents.msi hlavního uzlu.
1. Postupujte podle pokynů v průvodci k instalaci webových komponent

**Ke konfiguraci webových komponent**

1. Hlavního uzlu spusťte prostředí HPC PowerShell jako správce.
1. Chcete-li změnit adresář na umístění konfigurační skript, zadejte následující příkaz:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
1. Nakonfigurujte rozhraní REST a spuštění webové služby HPC, zadejte následující příkaz:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
1. Po zobrazení výzvy k výběru certifikátu, zvolte certifikát, který odpovídá veřejný název DNS hlavního uzlu. Například pokud provádíte nasazení hlavního uzlu virtuálního počítače pomocí modelu nasazení classic, název certifikátu vypadá CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Pokud používáte model nasazení Resource Manager, název certifikátu vypadá CN =&lt;*HeadNodeDnsName*&gt;.&lt; *oblasti*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Později vyberete tento certifikát při odesílání úloh k hlavnímu uzlu v místním počítači. Nemusíte vybrat nebo nakonfigurovat certifikát, který odpovídá názvu počítače k hlavnímu uzlu v doméně služby Active Directory (například CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
1. Pokud chcete nakonfigurovat webový portál pro odeslání úlohy, zadejte následující příkaz:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
1. Po dokončení skriptu, zastavte a restartujte služby Scheduleru úloh HPC zadáním následujících příkazů:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Krok 2: Instalace klienta nástroje sady HPC Pack na místním počítači
Pokud chcete nainstalovat klienta nástroje sady HPC Pack ve vašem počítači, stáhnout instalační soubory sady HPC Pack (úplná instalace) z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Při zahájení instalace zvolte možnost instalační program **klientské nástroje sady HPC Pack**.

Použití klientské nástroje sady HPC Pack odesílat úlohy k hlavnímu uzlu virtuálního počítače, potřebujete také exportovat certifikát z hlavního uzlu a instalaci na klientský počítač. Certifikát musí být v. Formátu CER.

**Exportujte certifikát z hlavního uzlu**

1. Hlavního uzlu přidáte modul snap-in Certifikáty do konzoly Microsoft Management Console pro účet místního počítače. Postup přidání modulu snap-in, naleznete v tématu [přidat modul Snap-in Certifikáty do konzoly MMC](https://technet.microsoft.com/library/cc754431.aspx).
1. Ve stromové struktuře konzoly rozbalte **certifikáty-místní počítač** > **osobní**a potom klikněte na tlačítko **certifikáty**.
1. Vyhledejte certifikát, který jste nakonfigurovali pro web součásti sady HPC Pack v [krok 1: instalace a konfigurace webové komponenty hlavního uzlu](#step-1-install-and-configure-the-web-components-on-the-head-node) (například CN =&lt;*HeadNodeDnsName* &gt;. cloudapp.net).
1. Klikněte pravým tlačítkem na certifikát a klikněte na tlačítko **všechny úkoly** > **exportovat**.
1. V Průvodci exportem certifikátu klikněte na tlačítko **Další**a ujistěte se, že **Ne, neexportovat privátní klíč** zaškrtnuto.
1. Postupujte podle pokynů průvodce a vyexportujte certifikát v binární kódování DER X.509 (. Formátu CER).

**Chcete-li importovat certifikát na klientském počítači**

1. Zkopírujte certifikát, který jste exportovali z hlavního uzlu do složky v klientském počítači.
1. Na klientském počítači spusťte certmgr.msc.
1. Ve Správci certifikátů rozbalte **certifikáty – aktuální uživatel** > **důvěryhodných kořenových certifikačních autorit**, klikněte pravým tlačítkem na **certifikáty**a pak Klikněte na tlačítko **všechny úkoly** > **Import**.
1. V Průvodci importem certifikátu klikněte na tlačítko **Další** a postupujte podle kroků pro import certifikátu, který jste exportovali z hlavního uzlu do úložiště Důvěryhodné kořenové certifikační autority.

> [!TIP]
> Může se zobrazit upozornění zabezpečení, protože certifikační autority na hlavní uzel nebyl rozpoznán v klientském počítači. Pro účely testování, můžete toto upozornění ignorovat a provést import certifikátu.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Krok 3: Spuštění testu úloh na clusteru
Pokud chcete ověřit konfiguraci, zkuste probíhajících úloh na clusteru v Azure z místního počítače. Například můžete použít nástroje pro prostředí HPC Pack grafického uživatelského rozhraní nebo příkazového řádku příkazy odesílat úlohy do clusteru. Webový portál můžete také použít k odesílání úloh.

**Ke spuštění příkazů odeslání úloh v klientském počítači**

1. Na klientském počítači, kde jsou nainstalovány nástroje sady HPC Pack klienta spusťte příkazový řádek.
1. Zadejte ukázka příkazu. Seznam všech úloh na clusteru, zadejte například příkaz podobný jednu z těchto možností podle toho úplný název DNS hlavního uzlu:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    nebo
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Použijte úplný název DNS hlavního uzlu není IP adresa, v adrese URL plánovače. Pokud zadáte IP adresu, chybu, zobrazí se podobný "certifikát serveru musí mít buď platný řetěz certifikátů nebo umístit do úložiště důvěryhodných kořenových."
   > 
   > 
1. Po zobrazení výzvy zadejte uživatelské jméno (ve formě &lt;DomainName&gt;\\&lt;uživatelské jméno&gt;) a heslo správce clusteru HPC nebo jinému uživateli clusteru, který jste nakonfigurovali. Můžete se k ukládání přihlašovacích údajů pro operace další úlohy.
   
    Zobrazí se seznam úloh.

**Použití Správce úloh HPC na klientském počítači**

1. Pokud při odeslání úlohy nebyl dříve ukládat přihlašovací údaje domény pro uživatele clusteru, můžete přidat pověření do správce přihlašovacích údajů.
   
    a. V Ovládacích panelech klientského počítače spusťte Správce přihlašovacích údajů.
   
    b. Klikněte na tlačítko **přihlašovací údaje Windows** > **přidat přihlašovací údaj obecný**.
   
    c. Zadejte adresu Internetu (třeba https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler nebo https://&lt;HeadNodeDnsName&gt;.&lt; oblast&gt;.cloudapp.azure.com/HpcScheduler) a uživatelské jméno (&lt;DomainName&gt;\\&lt;uživatelské jméno&gt;) a heslo Správce clusterů nebo jiného uživatele clusteru, který jste nakonfigurovali.
1. Na klientském počítači spusťte Správce úloh HPC.
1. V **vyberte hlavní uzel** dialogovém okně zadejte adresu URL k hlavnímu uzlu v Azure (třeba https://&lt;HeadNodeDnsName&gt;. cloudapp.net a https://&lt;HeadNodeDnsName&gt;.&lt; oblast&gt;. cloudapp.azure.com).
   
    Správce úloh HPC se otevře a zobrazí seznam úloh hlavního uzlu.

**Použít webový portál využívající k hlavnímu uzlu**

1. Na klientském počítači spusťte webový prohlížeč a zadejte jednu z následujících adres, v závislosti na úplný název DNS hlavního uzlu:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    nebo
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
1. V dialogovém okně zabezpečení zadejte domény přihlašovací údaje Správce clusteru HPC. (Můžete také přidat další uživatele clusteru v různých rolích. Zobrazit [správy clusteru uživatelů](https://technet.microsoft.com/library/ff919335.aspx).)
   
    Webový portál otevře zobrazení seznamu úloh.
1. Odeslání vzorku úlohy, která vrací řetězec "Hello World" z clusteru, klikněte na tlačítko **nová úloha** v levém navigačním panelu.
1. Na **nová úloha** stránce v části **od odeslání stránky**, klikněte na tlačítko **HelloWorld**. Zobrazí se stránka odeslání úlohy.
1. Klikněte na tlačítko **odeslat**. Pokud se zobrazí výzva, zadejte přihlašovací údaje domény na správce clusteru HPC. Úloha odeslána, a zobrazí se mu ID úlohy v **Moje úlohy** stránky.
1. Chcete-li zobrazit výsledky úlohy, které jste odeslali, klikněte na úlohu s ID a potom klikněte na tlačítko **úlohy v zobrazení** Chcete-li zobrazit výstup příkazu (v části **výstup**).

## <a name="next-steps"></a>Další postup
* Mohou také odesílat úlohy do clusteru Azure se [rozhraní REST API služby HPC Pack](https://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Pokud chcete odesílat úlohy clusteru z klienta systému Linux, přečtěte si ukázku Pythonu v [HPC Pack 2012 R2 SDK a ukázkový kód](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
