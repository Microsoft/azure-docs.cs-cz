---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2df08968ad66bd330611b975c045c9e9c9b240aa
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226550"
---
V závislosti na prostředí a možnosti můžete vytvořit skript všechny clusteru infrastrukturu, včetně virtuální sítě Azure, účty úložiště, cloudové služby, řadič domény, vzdálené nebo místní databáze SQL, hlavní uzel a další uzly clusteru. Skript můžete také použít už existující infrastrukturu Azure a vytvořit pouze uzly clusteru prostředí HPC.

Základní informace o plánování clusteru HPC Pack, najdete v článku [období pro vyzkoušení produktu a plánování](https://technet.microsoft.com/library/jj899596.aspx) a [Začínáme](https://technet.microsoft.com/library/jj899590.aspx) obsahu v knihovně TechNet HPC Pack 2012 R2.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**: předplatné můžete použít ve službě Azure Global nebo Azure China. Vaše předplatné omezení ovlivnit počet a typ uzlů clusteru, které můžete nasadit. Informace najdete v tématu [předplatného Azure a limity, kvóty a omezení](../articles/azure-subscription-service-limits.md).
* **Windows klientského počítače pomocí Azure Powershellu 0.8.10 nebo novější nainstalován a nakonfigurován**: viz [Začínáme s Azure Powershellem](/powershell/azureps-cmdlets-docs) pokyny k instalaci a kroky pro připojení k vašemu předplatnému Azure.
* **Skriptem nasazení IaaS sady HPC Pack**: stažení a rozbalení nejnovější verzi skriptu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Zkontrolujte verzi skriptu spuštěním `New-HPCIaaSCluster.ps1 –Version`. Tento článek je založen na verze 4.5.2 skriptu.
* **Konfigurační soubor skriptu**: Vytvořte soubor XML, který tento skript používá ke konfiguraci clusteru prostředí HPC. Informace a příklady najdete v částech dále v tomto článku a soubor Manual.rtf, který doprovází skript nasazení.

## <a name="syntax"></a>Syntaxe
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Spusťte skript jako správce.
> 
> 

### <a name="parameters"></a>Parametry
* **ConfigFile**: Určuje cestu souboru konfigurace pro popis clusteru prostředí HPC. Další informace o konfiguračním souboru v tomto tématu najdete v článku nebo v souboru Manual.rtf ve složce obsahující skript.
* **AdminUserName**: Určuje uživatelské jméno. Pokud domény doménové struktury je vytvořený skript, bude uživatelské jméno místního správce pro všechny virtuální počítače a název správce domény. Pokud domény doménové struktury už existuje, určuje uživatele domény jako uživatelské jméno místního správce k instalaci sady HPC Pack.
* **AdminPassword**: Určuje heslo správce. Pokud není zadán v příkazovém řádku, skript vás vyzve k zadání hesla.
* **HPCImageName** (volitelné): Určuje název image virtuálního počítače HPC Pack použít k nasazení clusteru prostředí HPC. Musí být poskytovaný společností Microsoft HPC Pack image z Azure Marketplace. Pokud se nezadá (doporučeno obvykle), skript vybere nejnovější publikované [image HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). Nejnovější image založena na Windows Server 2012 R2 Datacenter s HPC Pack 2012 R2 Update 3 nainstalované.
  
  > [!NOTE]
  > Pokud nechcete zadat platná bitová kopie sady HPC Pack, nasazení se nezdaří.
  > 
  > 
* **Soubor protokolu** (volitelné): Určuje cestu k souboru protokolu nasazení. Pokud není zadán, skript vytvoří soubor protokolu v adresáři temp počítače při spuštění skriptu.
* **Platnost** (volitelné): potlačí všechny výzvy k potvrzení.
* **NoCleanOnFailure** (volitelné): Určuje, že virtuální počítače Azure, které nejsou úspěšné nasazení se neodeberou. Odebrat tyto virtuální počítače ručně před opětovným spuštěním skriptu pokračujte nasazení nebo nasazení může selhat.
* **PSSessionSkipCACheck** (volitelné): pro každou cloudovou službu s tímto skriptem nasazených virtuálních počítačů, je certifikát podepsaný svým držitelem automaticky generuje služba Azure, a všechny virtuální počítače v cloudové službě použít tento certifikát jako výchozí vzdálené Windows Certifikát pro správu (WinRM). Nasazení funkce HPC na těchto virtuálních počítačích Azure, ve výchozím nastavení tento skript dočasně nainstaluje tyto certifikáty v místním počítači\\úložiště důvěryhodných kořenových certifikačních autorit v klientském počítači můžete potlačit chyby zabezpečení "není důvěryhodné certifikační Autority" Při provádění skriptu. Certifikáty se odeberou po dokončení skriptu. Pokud tento parametr zadán, v klientském počítači nejsou nainstalovány certifikáty a potlačit upozornění zabezpečení.
  
  > [!IMPORTANT]
  > Tento parametr se nedoporučuje pro nasazení v produkčním prostředí.
  > 
  > 

### <a name="example"></a>Příklad:
Následující příklad vytvoří cluster prostředí HPC Pack je používán konfigurační soubor *MyConfigFile.xml*a určuje přihlašovací údaje správce pro instalaci clusteru.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Další aspekty
* Skript můžete volitelně povolit odeslání úlohy pomocí webového portálu HPC Pack nebo prostředí HPC Pack REST API.
* Skript lze volitelně spustit skripty před a po konfiguraci hlavního uzlu Pokud chcete nainstalovat další software nebo nakonfigurovat další nastavení.

## <a name="configuration-file"></a>Konfigurační soubor
Konfigurační soubor pro skript nasazení je soubor XML. Soubor schématu HPCIaaSClusterConfig.xsd probíhá složka se skriptem nasazení IaaS sady HPC Pack. **IaaSClusterConfig** je kořenovým prvkem konfiguračního souboru, který obsahuje podřízené prvky podrobně popsány v souboru Manual.rtf ve složce nasazení skriptu.

