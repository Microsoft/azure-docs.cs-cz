---
title: Odeslání úlohy HPC Pack clusteru v Azure | Microsoft Docs
description: Zjistěte, jak nastavit na místní počítač k odesílání úloh do clusteru HPC Pack v Azure
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
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: 263946c1a1bd792b2f23a55388b73a82ddad0000
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Odeslání úloh HPC z místního počítače do clusteru HPC Pack nasazeného v Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Konfigurace klientského počítače k odesílání úloh do k místní [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) clusteru v Azure. Tento článek ukazuje, jak nastavit místního počítače s klientskými nástroji se odeslat úlohu přes HTTPS do clusteru v Azure. Tímto způsobem můžete několika uživatelům clusteru odesílání úloh do clusteru HPC Pack založené na cloudu, ale bez připojení přímo k hlavnímu uzlu virtuálního počítače nebo přístup k předplatnému Azure.

![Odeslání úlohy do clusteru s podporou v Azure][jobsubmit]

## <a name="prerequisites"></a>Požadavky
* **Nasadit virtuální počítač Azure hlavního uzlu HPC Pack** -doporučujeme použít automatizované nástroje, jako [šablony Azure rychlý Start](https://azure.microsoft.com/documentation/templates/) nebo [skript prostředí Azure PowerShell](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) nasazení hlavního uzlu a clusteru . Potřebujete název DNS hlavního uzlu a přihlašovací údaje Správce clusteru k dokončení kroků v tomto článku.
* **Klientský počítač** -potřebujete klientského počítače Windows nebo Windows Server, který můžete spustit HPC Pack klienta nástroje (viz [požadavky na systém](https://technet.microsoft.com/library/dn535781.aspx)). Pokud chcete používat k odesílání úloh HPC Pack webový portál nebo REST API, můžete použít libovolného klientského počítače podle svého výběru.
* **HPC Pack instalačním médiu** – k instalaci nástroje klienta HPC Pack volné instalační balíček pro nejnovější verzi sady HPC Pack (HPC Pack 2012 R2) je k dispozici z [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=328024). Ujistěte se, že si stáhnout stejnou verzi nástroje HPC Pack, který je nainstalován na hlavního uzlu virtuálního počítače.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Krok 1: Instalace a konfigurace webové komponenty hlavního uzlu
Povolit rozhraní REST k odesílání úloh do clusteru pomocí protokolu HTTPS, nakonfigurujte webové komponenty HPC Pack hlavního uzlu HPC Pack. Pokud již nejsou nainstalovány, nejprve nainstalujte webové komponenty spuštěním HpcWebComponents.msi instalační soubor. Potom nakonfigurujte komponenty spuštěním skriptu prostředí HPC PowerShell **Set-HPCWebComponents.ps1**.

Podrobné postupy najdete v tématu [nainstalovat webové komponenty Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Některé šablony Azure rychlý start pro HPC Pack instalace a konfigurace webové komponenty automaticky. Pokud použijete [skript nasazení HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) k vytvoření clusteru, můžete volitelně nainstalovat a nakonfigurovat webové komponenty jako součást nasazení.
> 
> 

**Chcete-li nainstalovat webové komponenty**

1. Připojte k hlavnímu uzlu virtuálního počítače s použitím pověření správce clusteru.
2. Ve složce instalace sady HPC Pack spusťte z hlavního uzlu HpcWebComponents.msi.
3. Postupujte podle kroků v průvodci k instalaci webové komponenty

**Konfigurace webové komponenty**

1. Z hlavního uzlu spusťte prostředí HPC PowerShell jako správce.
2. Chcete-li změnit adresář, do umístění souboru konfigurační skript, zadejte následující příkaz:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Nakonfigurujte rozhraní REST a spuštění služby webové prostředí HPC, zadejte následující příkaz:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Po zobrazení výzvy k výběru certifikátu, vyberte certifikát, který odpovídá názvu DNS veřejné hlavního uzlu. Například pokud nasadíte hlavního uzlu virtuálního počítače pomocí modelu nasazení classic, název certifikátu vypadá CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Pokud používáte model nasazení Resource Manager, název certifikátu vypadá CN =&lt;*HeadNodeDnsName*&gt;.&lt; *oblast*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Můžete vybrat tento certifikát později při odesílání úlohy k hlavnímu uzlu z místního počítače. Nevyberete ani nakonfigurovat certifikát, který odpovídá názvu počítače hlavního uzlu v doméně služby Active Directory (například CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. Pokud chcete nakonfigurovat na webový portál pro úlohy, zadejte následující příkaz:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Po dokončení skriptu, zastavte a restartujte služby plánovače úloh HPC zadáním následujících příkazů:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Krok 2: Instalace klienta nástroje HPC Pack na místním počítači
Pokud chcete nainstalovat klienta nástroje HPC Pack ve vašem počítači, stáhnout instalační soubory HPC Pack (úplná instalace) z [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=328024). Abyste před zahájením instalace, zvolte možnost instalační program **HPC Pack klienta nástroje**.

Pomocí nástrojů sady HPC Pack klienta k odesílání úloh do hlavního uzlu virtuálního počítače, musíte také exportovat certifikát z hlavního uzlu a nainstalujte ji na klientském počítači. Certifikát musí být v. Formátu CER.

**Export certifikátu z hlavního uzlu**

1. Z hlavního uzlu přidáte modul snap-in Certifikáty do konzoly Microsoft Management Console pro účet místního počítače. Postup pro přidání modulu snap-in, najdete v části [přidat modul Snap-in Certifikáty do konzoly MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. Ve stromu konzoly rozbalte **certifikáty – místní** > **osobní**a potom klikněte na **certifikáty**.
3. Vyhledejte certifikát, který jste nakonfigurovali pro komponenty webové HPC Pack [krok 1: instalace a konfigurace webové komponenty hlavního uzlu](#step-1:-install-and-configure-the-web-components-on-the-head-node) (například CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).
4. Pravým tlačítkem na certifikát a klikněte na tlačítko **všechny úlohy** > **exportovat**.
5. V Průvodci exportem certifikátu klikněte na **Další**a ujistěte se, že **Ne, neexportovat soukromý klíč** je vybrána.
6. Postupujte podle zbývajících kroků tohoto průvodce můžete exportovat certifikát v binární kódování DER X.509 (. Formátu CER).

**Při importu certifikátu v klientském počítači**

1. Zkopírujte certifikát, který jste exportovali z hlavního uzlu do složky v klientském počítači.
2. Na klientském počítači spusťte certmgr.msc.
3. Ve Správci certifikátů rozbalte **certifikáty – aktuální uživatel** > **důvěryhodné kořenové certifikační autority**, klikněte pravým tlačítkem na **certifikáty**a potom klikněte na **všechny úlohy** > **Import**.
4. V Průvodci importem certifikátu klikněte na **Další** a postupujte podle kroků pro import certifikátu, který jste exportovali z hlavního uzlu do úložiště důvěryhodných kořenových certifikačních autorit.

> [!TIP]
> Může se zobrazit upozornění zabezpečení, protože nebyla rozpoznána certifikační autority z hlavního uzlu v klientském počítači. Pro účely testování můžete toto upozornění ignorovat a import certifikátu dokončit.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Krok 3: Spuštění testovací úlohy v clusteru
Pokud chcete ověřit konfiguraci, zkuste spustit úlohy v clusteru v Azure z místního počítače. Můžete například použít HPC Pack grafického uživatelského rozhraní nástroje nebo příkazy příkazového řádku k odesílání úloh do clusteru. Webový portál můžete taky použít k odesílání úloh.

**Ke spuštění úlohy odesílání příkazů v klientském počítači**

1. Na klientském počítači, kde jsou nainstalovány nástroje sady HPC Pack klienta spusťte příkazový řádek.
2. Zadejte příkaz Ukázka. Například pro zobrazení seznamu všech úloh v clusteru, zadejte příkaz podobný jednu z těchto, v závislosti na úplný název DNS hlavního uzlu:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    nebo
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Úplný název DNS hlavního uzlu, ne IP adresy, použijte v adrese URL plánovače. Pokud zadáte IP adresu, chyba se zobrazí podobná "certifikát serveru musí mít buď platný řetěz certifikátů nebo umístit do úložiště důvěryhodných kořenových."
   > 
   > 
3. Po zobrazení výzvy zadejte uživatelské jméno (ve formě &lt;DomainName&gt;\\&lt;uživatelské jméno&gt;) a heslo správce clusteru HPC nebo jiný uživatel clusteru, který jste nakonfigurovali. Můžete k uložení pověření místně pro další operace úlohy.
   
    Zobrazí se seznam úloh.

**Na klientském počítači používat Správce úloh HPC**

1. Pokud při odesílání úlohy nebyla dříve ukládat přihlašovací údaje domény pro uživatele clusteru, můžete přidat přihlašovací údaje do správce přihlašovacích údajů.
   
    a. V Ovládacích panelech klientského počítače spusťte Správce přihlašovacích údajů.
   
    b. Klikněte na tlačítko **pověření systému Windows** > **přidat obecné přihlašovací údaje**.
   
    c. Zadejte internetovou adresu (například https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler nebo https://&lt;HeadNodeDnsName&gt;.&lt; oblast&gt;.cloudapp.azure.com/HpcScheduler) a uživatelské jméno (&lt;DomainName&gt;\\&lt;uživatelské jméno&gt;) a heslo správce clusteru nebo jiný uživatel clusteru, který jste nakonfigurovali.
2. Na klientském počítači spusťte Správce úloh HPC.
3. V **vyberte hlavní uzel** dialogu, zadejte adresu URL k hlavnímu uzlu v Azure (například https://&lt;HeadNodeDnsName&gt;. cloudapp.net nebo https://&lt;HeadNodeDnsName&gt;.&lt; oblast&gt;. cloudapp.azure.com).
   
    Správce úloh HPC otevře a zobrazí se seznam úloh z hlavního uzlu.

**Použití webového portálu systémem hlavního uzlu**

1. Na klientském počítači spustit webový prohlížeč a zadejte jednu z následujících adres, v závislosti na úplný název DNS hlavního uzlu:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    nebo
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. V dialogovém okně zabezpečení, který se zobrazí zadejte přihlašovací údaje Správce clusteru HPC domény. (Můžete také přidat další clusteru uživatele v různých rolích. V tématu [Správa uživatelů clusteru](https://technet.microsoft.com/library/ff919335.aspx).)
   
    Na webový portál se otevře zobrazení seznamu úloh.
3. K odeslání vzorku úlohu, která vrátí řetězec "Hello World" z clusteru, klikněte na tlačítko **nová úloha** v levém navigačním panelu.
4. Na **nová úloha** v části **ze stránek odeslání**, klikněte na tlačítko **HelloWorld**. Zobrazí se stránka odeslání úlohy.
5. Klikněte na tlačítko **odeslání**. Pokud se zobrazí výzva, zadejte přihlašovací údaje domény Správce clusteru HPC. Je úloha odeslána a ID úlohy se zobrazí na **Mé úlohy** stránky.
6. Pokud chcete zobrazit výsledky úlohy, které jste odeslali, klikněte na úlohu s ID a potom klikněte na **úlohy v zobrazení** k zobrazení výstupu příkazu (v části **výstup**).

## <a name="next-steps"></a>Další postup
* Můžete také odesílání úloh do clusteru Azure s [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Pokud chcete k odesílání úloh clusteru z klienta Linux, viz ukázka Pythonu ve [HPC Pack 2012 R2 SDK a ukázkový kód](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
