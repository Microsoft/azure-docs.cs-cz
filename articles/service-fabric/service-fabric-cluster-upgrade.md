---
title: Upgrade clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Upgrade kódu Service Fabric a/nebo konfigurace, která se spouští cluster Service Fabric, včetně nastavení režimu aktualizace clusteru upgrade certifikáty, přidání aplikace portů, provádění oprav operačního systému, a tak dále. Co můžete očekávat, když probíhají upgrady?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 2fd62f8709bddfd981f4b1358c97d0acbaf7f12d
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269094"
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Upgrade clusteru Azure Service Fabric
> [!div class="op_single_selector"]
> * [Azure Cluster](service-fabric-cluster-upgrade.md)
> * [Samostatný Cluster](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Návrh pro zdokonalovány moderního systému, je klíčem k dosažení dlouhodobý úspěch vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně spravovaného společností Microsoft. Tento článek popisuje, co se automaticky spravuje a co můžete nakonfigurovat sami.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze prostředků infrastruktury, na kterém běží v clusteru
Můžete nastavit pro příjem upgradů automatické prostředků infrastruktury, jako jsou vydané společností Microsoft nebo můžete vybrat verzi podporovaných prostředků infrastruktury, že chcete, aby váš cluster na clusteru.

To provedete nastavením konfigurace clusteru "upgradeMode" na portálu nebo pomocí Resource Manageru v době vytváření nebo později na aktivní cluster 

> [!NOTE]
> Ujistěte se, že zachovat cluster vždy používá verzi podporovanou prostředků infrastruktury. Jak a kdy jsme oznamujeme vydání nové verze service Fabric, předchozí verze budou označena k ukončení podpory po minimálně za 60 dní od data. Oznámení nových vydaných verzích [na blog týmu service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Vyberete, pak k dispozici je nová verze. 
> 
> 

14 dní před vypršení platnosti vydané verze, po který váš cluster běží, vygeneruje událost stavu, který váš cluster přepne do stavu upozornění. Cluster zůstane ve varovném stavu, dokud neprovedete upgrade na verzi podporovaných prostředků infrastruktury.

### <a name="setting-the-upgrade-mode-via-portal"></a>Nastavení režimu upgradu prostřednictvím portálu
Cluster můžete nastavit na automatické nebo ruční při vytváření clusteru.

![Create_Manualmode][Create_Manualmode]

Cluster můžete nastavit na automatické nebo ruční na aktivní cluster pomocí spravovat prostředí. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgrade na novou verzi na cluster, který je nastavený na ruční režim prostřednictvím portálu.
Upgrade na novou verzi, vše, co je třeba provést je z rozevíracího seznamu vyberte verzi k dispozici a uložte. Upgrade pro Fabric získá spustila automaticky. Zásady stavu clusteru (kombinace uzlu stav a stav všechny aplikace spuštěné v clusteru) jsou zachovány během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Přejděte na tento dokument Další informace o tom, jak nastavit tyto zásady vlastní stavu. 

Po opravě problémů, z kterých vzniklo vrácení změn potřebujete k zahájení upgradu znovu podle stejných kroků jako před.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Nastavení režimu upgradu pomocí šablony Resource Manageru
Přidat konfiguraci "upgradeMode" v definici zdrojů Microsoft.ServiceFabric/clusters a nastavit "clusterCodeVersion" na jednu z podporovaných fabric verzí, jak je znázorněno níže a potom šablonu nasadit. Platné hodnoty pro "upgradeMode" jsou "Ruční" nebo "Automatické"

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgrade na novou verzi na cluster, který je nastavený na ruční režim pomocí šablony Resource Manageru.
Pokud je cluster v režimu ručního, upgrade na novou verzi, změňte "clusterCodeVersion" na podporovanou verzi a nasaďte ji. Nasazení šablony, zejména kopance upgradu prostředků infrastruktury získá spustila automaticky. Zásady stavu clusteru (kombinace uzlu stav a stav všechny aplikace spuštěné v clusteru) jsou zachovány během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Přejděte na tento dokument Další informace o tom, jak nastavit tyto zásady vlastní stavu. 

Po opravě problémů, z kterých vzniklo vrácení změn potřebujete k zahájení upgradu znovu podle stejných kroků jako před.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Získání seznamu všech verzi k dispozici pro všechna prostředí pro daný odběr
Spusťte následující příkaz a měli byste obdržet výstup podobný tomuto.

říká "supportExpiryUtc" Pokud dané vydání vyprší platnost nebo vypršela platnost. Nejnovější verze neobsahuje platné datum – má hodnotu "9999-12-31T23:59:59.9999999", což znamená jen, že datum vypršení platnosti zatím není nastavená.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Fabric upgradu chování při upgradu režimu clusteru je automatické
Společnost Microsoft udržuje kód prostředků infrastruktury a konfigurace, která běží v clusteru Azure. Provádíme automatické monitorované upgrady softwaru podle potřeby. Tyto upgrady může být kód, konfigurace nebo obojí. Pokud chcete mít jistotu, že vaše aplikace odkážete žádný dopad nebo minimální dopad na tyto upgrady, jsme upgrady provádět v následujících fázích:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fáze 1: Upgrade se provádí pomocí všechny zásady stavu clusteru
V této fázi upgrady pokračovat jednu upgradovací doménu najednou a aplikace, které byly spuštěny v clusteru dál běžet bez jakýchkoli prostojů. Zásady stavu clusteru (kombinace uzlu stav a stav všechny aplikace spuštěné v clusteru) jsou zachovány během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Potom se vlastník předplatného odešle e-mail. E-mailu obsahuje následující informace:

* Oznámení, že jsme museli vrácení upgradu clusteru.
* Navrhované nápravné akce, pokud existuje.
* Počet dní (n), dokud se provede fázi 2.

Snažíme se provést stejný upgrade několik víckrát v případě, že všechny aktualizace se nezdařilo z důvodů infrastruktury. Po n dní od data, kdy byla vyslána e-mailu můžeme přejít k fázi 2.

Pokud jsou splněny zásady stavu clusteru, upgrade považuje za úspěšnou a označí dokončené. To může nastat během počáteční upgrade nebo některý z upgradu opakování v této fázi. Neexistuje žádný e-mailové potvrzení úspěšného spuštění. Tím se vyhnout odesílání, že jste příliš moc e-mailů – příjem e-mailu měla by se zobrazit jako výjimku do normální. Očekáváme, že většina upgrady clusteru uspět bez dopadu na dostupnost vaší aplikace.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fáze 2: Upgrade se provádí pomocí výchozí stav pouze zásady
Stav zásady v této fázi jsou nastavené tak, že počet aplikací, které byly v dobrém stavu na začátku upgradu, zůstává stejná po dobu trvání procesu upgradu. Stejně jako v fáze 1 fáze 2 upgrady pokračovat jednu upgradovací doménu najednou a aplikace, které byly spuštěny v clusteru dál běžet bez jakýchkoli prostojů. Zásady stavu clusteru (kombinace uzlu stav a stav všechny aplikace spuštěné v clusteru) jsou zachovány po dobu trvání upgradu.

Pokud zásady stavu clusteru v platnosti nejsou splněny, upgrade se vrátí zpět. Potom se vlastník předplatného odešle e-mail. E-mailu obsahuje následující informace:

* Oznámení, že jsme museli vrácení upgradu clusteru.
* Navrhované nápravné akce, pokud existuje.
* Počet dní (n), dokud se provede fáze 3.

Snažíme se provést stejný upgrade několik víckrát v případě, že všechny aktualizace se nezdařilo z důvodů infrastruktury. Několik dní, než n dní jsou v provozu se odešle e-mail s připomenutím. Po n dní od data, kdy byla vyslána e-mailu můžeme přejít k fáze 3. E-mailů, který jsme vám poslali ve fázi 2 musí být přijata vážně a nápravné akce musí být přijata.

Pokud jsou splněny zásady stavu clusteru, upgrade považuje za úspěšnou a označí dokončené. To může nastat během počáteční upgrade nebo některý z upgradu opakování v této fázi. Neexistuje žádný e-mailové potvrzení úspěšného spuštění.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fáze 3: Upgrade se provádí pomocí zásady agresivní stavu
Tyto zásady stavu v této fázi jsou zaměřené směrem k dokončení upgradu spíše než stavu aplikace. V této fázi skončit velmi málo upgrady clusteru. Pokud váš cluster dostane do této fáze, je velmi pravděpodobné, že vaše aplikace nebude v pořádku a/nebo ke ztrátě dostupnosti.

Podobně jako dvě fáze 3 fáze upgradu pokračovat jednu upgradovací doménu najednou.

Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Snažíme se provést stejný upgrade několik víckrát v případě, že všechny aktualizace se nezdařilo z důvodů infrastruktury. Potom se Připne clusteru, tak, aby se už nebude dostávat, podpory a/nebo upgrady.

Odešle e-mail s těmito informacemi na vlastníka předplatného, spolu s nápravné akce. Neočekáváme jakémkoli rychle dostat do stavu, ve kterém se nezdařilo fáze 3.

Pokud jsou splněny zásady stavu clusteru, upgrade považuje za úspěšnou a označí dokončené. To může nastat během počáteční upgrade nebo některý z upgradu opakování v této fázi. Neexistuje žádný e-mailové potvrzení úspěšného spuštění.

## <a name="cluster-configurations-that-you-control"></a>Konfigurace clusteru, které řídíte
Kromě schopnosti nastavit cluster režim upgradu, tady je konfigurace, které můžete změnit na aktivní cluster.

### <a name="certificates"></a>Certifikáty
Můžete přidat nové nebo snadno odstranit certifikáty pro klienta přes portál pro cluster. Odkazovat na [tento dokument pro podrobné pokyny](service-fabric-cluster-security-update-certs-azure.md)

![Snímek obrazovky zobrazující kryptografické otisky certifikátů na webu Azure Portal.][CertificateUpgrade]

### <a name="application-ports"></a>Porty aplikace
Porty aplikace můžete změnit změnou vlastnosti prostředku nástroje pro vyrovnávání zatížení, které jsou spojeny s typem uzlu. Můžete na portálu nebo Powershellu pro Resource Manager můžete použít přímo.

Chcete-li otevřít nový port pro všechny virtuální počítače v typu uzlu, postupujte takto:

1. Přidáte nový test odpovídající služba Vyrovnávání zatížení.
   
    Pokud jste nasadili cluster pomocí portálu, jsou nástroje pro vyrovnávání zatížení s názvem "LB-název skupiny prostředků – NodeTypename", jeden pro každý typ uzlu. Protože jsou pouze ve skupině prostředků jedinečné názvy nástroje pro vyrovnávání zatížení, je vhodné, pokud hledání v konkrétní skupině prostředků.
   
    ![Snímek obrazovky s nástroji pro vyrovnávání zatížení na portálu pro přidání testu.][AddingProbes]
2. Přidáte nové pravidlo nástroje pro vyrovnávání zatížení.
   
    Přidáte nové pravidlo stejné nástroje pro vyrovnávání zatížení pomocí testu, který jste vytvořili v předchozím kroku.
   
    ![Přidání nové pravidlo nástroje pro vyrovnávání zatížení na portálu.][AddingLBRules]

### <a name="placement-properties"></a>Vlastnosti umístění
Pro každý typ uzlu můžete přidat vlastní vlastnosti umístění, které chcete použít ve svých aplikacích. NodeType je ve výchozím nastavení, které můžete použít bez nutnosti ho explicitně přidávat.

> [!NOTE]
> Podrobnosti o použití omezení umístění, vlastnosti uzlu a postupy jejich definování najdete další informace naleznete v části "Umístění omezení a vlastnosti uzlu" v dokumentu Service Fabric Cluster Resource Manageru v [popisující clusteru](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Kapacitní metriky
Pro každý typ uzlu můžete přidat vlastní metriku kapacity, kterou chcete použít ve svých aplikacích k načtení sestavy. Podrobnosti týkající se použití metriky kapacity pro načtení sestavy, najdete v Service Fabric Cluster Resource Manager dokumenty na [popisující svůj Cluster](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Upgrade nastavení prostředků infrastruktury – stav zásady
Můžete určit, že stav vlastní zásady pro upgrade pro fabric. Pokud nastavíte váš cluster na upgrady prostředků infrastruktury automatické získat tyto zásady použít pro fázi 1 fabric automatické upgrady.
Pokud nastavíte cluster pro ruční fabric upgrady, získat tyto zásady použijí pokaždé, když vyberete možnost nové verze aktivuje systém pro pusťte se do upgradu prostředků infrastruktury ve vašem clusteru. Pokud není přepsat zásady, použijí se výchozí hodnoty.

Můžete zadat vlastní bezpečnostní zásady, případně zkontrolujte aktuální nastavení pod oknem "upgrade pro fabric" tak, že vyberete Upřesnit nastavení upgradu. Přečtěte si o tom, jak na následujícím obrázku. 

![Správa vlastní stav zásad][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Upravit nastavení prostředků infrastruktury pro cluster
Odkazovat na [service fabric nastavení prostředků infrastruktury clusteru](service-fabric-cluster-fabric-settings.md) toho, co a jak si můžete přizpůsobit.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Opravy operačního systému na virtuálních počítačích, které tvoří cluster
Odkazovat na [aplikace orchestraci oprav](service-fabric-patch-orchestration-application.md) které je možné nasadit v clusteru nainstalovat opravy ze služby Windows Update iniciovat organizovaně, což způsobem, udržování neustále dostupných služeb. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Upgrady operačního systému na virtuálních počítačích, které tvoří cluster
Pokud je nutné upgradovat image operačního systému na virtuálních počítačích clusteru, je nutné ho jeden virtuální počítač najednou. Jste zodpovědní pro tento upgrade – aktuálně nejsou k dispozici žádné automatizace pro tento.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak přizpůsobit některé z [service fabric nastavení prostředků infrastruktury clusteru](service-fabric-cluster-fabric-settings.md)
* Zjistěte, jak [dovnitř a ven škálování clusteru](service-fabric-cluster-scale-up-down.md)
* Další informace o [upgrady aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
