---
title: Upgrade clusteru Azure Service Fabric
description: Přečtěte si o upgradu verze nebo konfigurace clusteru Azure Service Fabric – nastavení režimu aktualizace clusteru, upgrade certifikátů, přidání portů aplikací, provádění oprav operačního systému a co můžete očekávat při provádění upgradů.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: 6897854820339fc78dd9083c82147dce95ab68b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258652"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Upgrade a aktualizace clusteru Azure Service Fabric

Pro každý moderní systém je návrh upgradability klíčem k dosažení dlouhodobého úspěchu vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně spravuje Microsoft. Tento článek popisuje, co se spravuje automaticky a co si můžete nakonfigurovat sami.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze prostředků infrastruktury, která běží v clusteru

Ujistěte se, že v clusteru je vždy spuštěna [podporovaná verze prostředků infrastruktury](service-fabric-versions.md). Pokaždé, když společnost Microsoft oznámí vydání nové verze service fabric, předchozí verze je označena pro ukončení podpory po minimálně 60 dnů od tohoto data. Nové verze jsou oznámeny na [blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dní před vypršením platnosti verze clusteru je spuštěna událost stavu, která umístí cluster do stavu upozornění. Cluster zůstane ve stavu upozornění, dokud neupgradujete na podporovanou verzi infrastruktury.

Můžete nastavit cluster přijímat automatické upgrady prostředků infrastruktury, jak jsou vydány společností Microsoft nebo můžete vybrat podporovanou verzi prostředků infrastruktury, kterou chcete, aby byl cluster zapnutý.  Další informace najdete v informacích o [inovaci verze service fabric clusteru](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Chování upgradu prostředků infrastruktury během automatických inovací

Microsoft udržuje kód prostředků infrastruktury a konfiguraci, která běží v clusteru Azure. Provádíme automatické monitorované upgrady softwaru podle potřeby. Tyto inovace může být kód, konfigurace nebo obojí. Chcete-li se ujistit, že vaše aplikace netrpí žádný dopad nebo minimální dopad v důsledku těchto upgradů, upgrady jsou prováděny v následujících fázích:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fáze 1: Upgrade se provádí pomocí všech zásad stavu clusteru

Během této fáze upgrady pokračovat jednu doménu upgradu najednou a aplikace, které byly spuštěny v clusteru nadále běžet bez prostojů. Zásady stavu clusteru (pro stav uzlu a stav aplikace) jsou dodržovány během upgradu.

Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět a odeslání e-mailu vlastníkovi předplatného. E-mail obsahuje následující informace:

* Oznámení, že jsme museli vrátit zpět upgrade clusteru.
* Navrhovaná nápravná opatření, pokud existuje.
* Počet dní (*n*) do provedení fáze 2.

Snažíme se provést stejný upgrade ještě několikrát v případě, že všechny upgrady se nezdařilo z důvodů infrastruktury. Po *n* dnech od data odeslání e-mailu přejdeme k fázi 2.

Pokud jsou splněny zásady stavu clusteru, upgrade je považován za úspěšný a označen jako dokončený. K tomu může dojít během počáteční inovace nebo některého z repríz upgradu v této fázi. Neexistuje žádné e-mailové potvrzení úspěšného spuštění. To je, aby se zabránilo odesílání příliš mnoho e-mailů; e-mail by měl být považován za výjimku z normálu. Očekáváme, že většina upgradů clusteru bude úspěšná, aniž by to mělo vliv na dostupnost vaší aplikace.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fáze 2: Upgrade se provádí pouze pomocí výchozích zásad stavu.

Zásady stavu v této fázi jsou nastaveny tak, aby počet aplikací, které byly v pořádku na začátku upgradu zůstává stejný po dobu trvání procesu upgradu. Stejně jako ve fázi 1, inovace fáze 2 pokračovat jeden upgrade domény najednou a aplikace, které byly spuštěny v clusteru nadále běžet bez prostojů. Zásady stavu clusteru (kombinace stavu uzlu a stavu všechny aplikace spuštěné v clusteru) jsou dodržovány po dobu trvání upgradu.

Pokud nejsou splněny zásady stavu clusteru ve skutečnosti nejsou splněny, upgrade je vrácena zpět. Potom je odeslán e-mail majiteli předplatného. E-mail obsahuje následující informace:

* Oznámení, že jsme museli vrátit zpět upgrade clusteru.
* Navrhovaná nápravná opatření, pokud existuje.
* Počet dní (n) do spuštění fáze 3.

Snažíme se provést stejný upgrade ještě několikrát v případě, že všechny upgrady se nezdařilo z důvodů infrastruktury. Připomenutí e-mail je odeslán pár dní před n dny jsou nahoru. Po n dnech od data odeslání e-mailu přejdeme k fázi 3. E-maily, které vám zašleme ve fázi 2, musí být brány vážně a musí být přijata nápravná opatření.

Pokud jsou splněny zásady stavu clusteru, upgrade je považován za úspěšný a označen jako dokončený. K tomu může dojít během počáteční inovace nebo některého z repríz upgradu v této fázi. Neexistuje žádné e-mailové potvrzení úspěšného spuštění.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fáze 3: Upgrade se provádí pomocí agresivní zásady stavu

Tyto zdravotní politiky v této fázi jsou zaměřeny na dokončení upgradu spíše než na stav aplikací. V této fázi skončí několik upgradů clusteru. Pokud se váš cluster dostane do této fáze, je pravděpodobné, že vaše aplikace přestane být v pořádku a/nebo ztratí dostupnost.

Podobně jako v ostatních dvou fázích, inovace fáze 3 pokračovat jeden upgrade domény najednou.

Pokud nejsou splněny zásady stavu clusteru, upgrade je vrácena zpět. Snažíme se provést stejný upgrade ještě několikrát v případě, že všechny upgrady se nezdařilo z důvodů infrastruktury. Poté je cluster připnutý, takže již nebude přijímat podporu nebo upgrady.

Vlastníkovi předplatného se odešle e-mail s těmito informacemi spolu s nápravnými akcemi. Neočekáváme, že všechny clustery se dostanou do stavu, ve kterém fáze 3 selhala.

Pokud jsou splněny zásady stavu clusteru, upgrade je považován za úspěšný a označen jako dokončený. K tomu může dojít během počáteční inovace nebo některého z repríz upgradu v této fázi. Neexistuje žádné e-mailové potvrzení úspěšného spuštění.

## <a name="manage-certificates"></a>Správa certifikátů

Service Fabric používá [certifikáty serveru X.509,](service-fabric-cluster-security.md) které zadáte při vytváření clusteru pro zabezpečení komunikace mezi uzly clusteru a ověřování klientů. Certifikáty pro cluster a klienta můžete přidávat, aktualizovat nebo odstraňovat na [webu Azure Portal](https://portal.azure.com) nebo pomocí příkazového příkazového příkazu PowerShell/Azure.  Další informace najdete v textu na [přidání nebo odebrání certifikátů.](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Otevření portů aplikací

Porty aplikace můžete změnit změnou vlastností prostředků vykladače zatížení, které jsou přidruženy k typu uzlu. Můžete použít portál Azure nebo můžete použít PowerShell/Azure CLI. Další informace naleznete v části [Otevřít porty aplikace pro cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definování vlastností uzlu

Někdy můžete chtít zajistit, že určité úlohy spustit pouze na určité typy uzlů v clusteru. Některé úlohy mohou například vyžadovat gpu nebo ssd disy, zatímco jiné nemusí. Pro každý z typů uzlů v clusteru můžete do uzlů clusteru přidat vlastní vlastnosti uzlu. Omezení umístění jsou příkazy připojené k jednotlivým službám, které vybírají pro jeden nebo více vlastností uzlu. Omezení umístění definují, kde by měly být služby spuštěny.

Podrobnosti o použití omezení umístění, vlastnosti uzlu a jejich definování, přečtěte si [vlastnosti uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Přidání metrik kapacity

Pro každý z typů uzlů můžete přidat vlastní metriky kapacity, které chcete použít ve vašich aplikacích k načtení sestavy. Podrobnosti o použití metrik kapacity pro načtení sestavy naleznete v dokumentech Správce prostředků clusteru Service Fabric v [popisu clusteru](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a načíst](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Nastavení zásad stavu pro automatické upgrady

Můžete zadat vlastní zásady stavu pro upgrade prostředků infrastruktury. Pokud jste nastavili cluster na automatické upgrady prostředků infrastruktury, tyto zásady se použijí na fázi 1 automatické upgrady prostředků infrastruktury.
Pokud jste nastavili cluster pro ruční upgrady prostředků infrastruktury, pak tyto zásady získat použít pokaždé, když vyberete novou verzi spouštění systému k zahájení upgradu prostředků infrastruktury v clusteru. Pokud nepřepíšete zásady, budou použity výchozí hodnoty.

Můžete určit vlastní zásady stavu nebo zkontrolovat aktuální nastavení v okně "upgrade prostředků infrastruktury" výběrem rozšířeného nastavení upgradu. Přečtěte si následující obrázek o tom, jak.

![Správa vlastních zásad stavu][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Přizpůsobení nastavení infrastruktury pro váš cluster

V clusteru lze přizpůsobit mnoho různých nastavení konfigurace, například úroveň spolehlivosti vlastností clusteru a uzlu. Další informace naleznete v [části Nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Oprava operačního systému v uzlech clusteru

Aplikace orchestrace oprav (POA) je aplikace Service Fabric, která automatizuje opravy operačního systému v clusteru Service Fabric bez prostojů. [Aplikace Orchestrace oprav pro Windows](service-fabric-patch-orchestration-application.md) lze nasadit do clusteru k instalaci oprav orchestrovaným způsobem při zachování služby k dispozici po celou dobu.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak přizpůsobit některá [nastavení clusteru prostředků infrastruktury prostředků služby](service-fabric-cluster-fabric-settings.md)
* Přečtěte si, jak [škálovat cluster y a zmenšování](service-fabric-cluster-scale-up-down.md)
* Informace o [upgradech aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
