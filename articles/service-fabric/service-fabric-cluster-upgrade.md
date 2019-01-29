---
title: Upgrade clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o upgradu na verzi nebo konfigurace clusteru služby Azure Service Fabric.  Tento článek popisuje nastavení aktualizace režimu clusteru, upgrade certifikáty, přidání portů aplikací, zajišťuje opravy operačního systému a co můžete očekávat při probíhají upgrady
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
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 2e5838046cdb60023495c7e9e111506c333cecc7
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102392"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Upgrade a aktualizuje se cluster Azure Service Fabric

Návrh pro zdokonalovány moderního systému, je klíčem k dosažení dlouhodobý úspěch vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně spravovaného společností Microsoft. Tento článek popisuje, co se automaticky spravuje a co můžete nakonfigurovat sami.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze prostředků infrastruktury, na kterém běží v clusteru

Ujistěte se, že zachovat cluster vždy používá verzi podporovanou prostředků infrastruktury. Jak a kdy jsme oznamujeme vydání nové verze service Fabric, předchozí verze budou označena k ukončení podpory po minimálně za 60 dní od data. Nové verze jsou zveřejněných na blog týmu service fabric. Vyberete, pak k dispozici je nová verze.

14 dní před vypršení platnosti vydané verze, po který váš cluster běží, vygeneruje událost stavu, který váš cluster přepne do stavu upozornění. Cluster zůstane ve varovném stavu, dokud neprovedete upgrade na verzi podporovaných prostředků infrastruktury.

Můžete nastavit pro příjem upgradů automatické prostředků infrastruktury, jako jsou vydané společností Microsoft nebo můžete vybrat verzi podporovaných prostředků infrastruktury, že chcete, aby váš cluster na clusteru.  Další informace najdete v článku [upgradovat verzi vašeho clusteru Service Fabric](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Chování při automatické upgrady upgradu prostředků infrastruktury
Společnost Microsoft udržuje kód prostředků infrastruktury a konfigurace, která běží v clusteru Azure. Provádíme automatické monitorované upgrady softwaru podle potřeby. Tyto upgrady může být kód, konfigurace nebo obojí. Pokud chcete mít jistotu, že vaše aplikace odkážete žádný dopad nebo minimální dopad na tyto upgrady, jsme upgrady provádět v následujících fázích:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fáze 1: Provádí se upgrade pomocí všechny zásady stavu clusteru
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

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fáze 3: Provádí se upgrade pomocí zásady agresivní stavu
Tyto zásady stavu v této fázi jsou zaměřené směrem k dokončení upgradu spíše než stavu aplikace. V této fázi skončit několik upgrady clusteru. Pokud váš cluster dostane do této fáze, je velmi pravděpodobné, že vaše aplikace nebude v pořádku a/nebo ke ztrátě dostupnosti.

Podobně jako dvě fáze 3 fáze upgradu pokračovat jednu upgradovací doménu najednou.

Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Snažíme se provést stejný upgrade několik víckrát v případě, že všechny aktualizace se nezdařilo z důvodů infrastruktury. Potom se Připne clusteru, tak, aby se už nebude dostávat, podpory a/nebo upgrady.

Odešle e-mail s těmito informacemi na vlastníka předplatného, spolu s nápravné akce. Neočekáváme jakémkoli rychle dostat do stavu, ve kterém se nezdařilo fáze 3.

Pokud jsou splněny zásady stavu clusteru, upgrade považuje za úspěšnou a označí dokončené. To může nastat během počáteční upgrade nebo některý z upgradu opakování v této fázi. Neexistuje žádný e-mailové potvrzení úspěšného spuštění.

## <a name="manage-certificates"></a>Správa certifikátů
Service Fabric používá [serverové certifikáty X.509](service-fabric-cluster-security.md) zadat při vytváření clusteru k zabezpečení komunikace mezi uzly clusteru a ověřování klientů. Můžete přidat, aktualizovat nebo odstranit certifikáty pro cluster a klienta v [webu Azure portal](https://portal.azure.com) nebo pomocí Powershellu nebo Azure CLI.  Další informace najdete v článku [přidat nebo odebrat certifikáty](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Porty otevřete aplikaci
Porty aplikace můžete změnit změnou vlastnosti prostředku nástroje pro vyrovnávání zatížení, které jsou spojeny s typem uzlu. Můžete použít na webu Azure portal nebo můžete pomocí Powershellu nebo Azure CLI. Další informace najdete v článku [otevřít porty aplikace pro cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definovat vlastnosti uzlu
Někdy můžete chtít Ujistěte se, že určité úlohy spouštěny pouze v určitých typů uzlů v clusteru. Pro některé úlohy může například vyžadovat grafickými procesory nebo disky SSD a jiné ne. Pro každý typ uzlu v clusteru můžete přidat vlastní uzel vlastnosti na uzlech clusteru. Omezení umístění jsou příkazy připojených služeb, které vyberte jeden nebo více vlastností uzlu. Omezení umístění definovat, ve kterém by měly běžet služby.

Podrobnosti o použití omezení umístění, vlastnosti uzlu a postupy jejich definování najdete [vlastnosti uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Přidání metriky kapacity
Pro každý typ uzlu můžete přidat vlastní metriku kapacity, kterou chcete použít ve svých aplikacích k načtení sestavy. Podrobnosti týkající se použití metriky kapacity pro načtení sestavy, najdete v Service Fabric Cluster Resource Manager dokumenty na [popisující svůj Cluster](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Nastavení zásad stavu na automatické upgrady
Můžete zadat vlastní stavu zásady pro upgrade pro fabric. Pokud nastavíte váš cluster na upgrady prostředků infrastruktury automatické získat tyto zásady použít pro fázi 1 fabric automatické upgrady.
Pokud nastavíte cluster pro ruční fabric upgrady, získat tyto zásady použijí pokaždé, když vyberete možnost nové verze aktivuje systém pro pusťte se do upgradu prostředků infrastruktury ve vašem clusteru. Pokud není přepsat zásady, použijí se výchozí hodnoty.

Můžete zadat vlastní bezpečnostní zásady, případně zkontrolujte aktuální nastavení pod oknem "upgrade pro fabric" tak, že vyberete Upřesnit nastavení upgradu. Přečtěte si o tom, jak na následujícím obrázku. 

![Správa vlastní stav zásad][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Upravit nastavení prostředků infrastruktury pro cluster
Mnoho různých konfiguračních nastavení se dají přizpůsobit v clusteru, jako je například úroveň spolehlivosti clusteru a uzlů vlastností. Další informace najdete v článku [nastavení prostředků infrastruktury pro cluster Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Opravy operačního systému v uzlech clusteru
Aplikace orchestraci oprav (POA) je aplikace Service Fabric, který automatizuje operačního systému, použití dílčích oprav v clusteru Service Fabric bez jakýchkoli prostojů. [Opravy Orchestrace aplikace pro Windows](service-fabric-patch-orchestration-application.md) nebo [aplikace orchestraci oprav pro Linux](service-fabric-patch-orchestration-application-linux.md) je možné nasadit v clusteru pro instalaci oprav iniciovat organizovaně, což způsobem při zachování služeb dostupné neustále. 

## <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Upgrady operačního systému na virtuálních počítačích, které tvoří cluster
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
