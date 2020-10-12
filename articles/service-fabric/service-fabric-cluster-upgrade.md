---
title: Upgrade clusteru Azure Service Fabric
description: Přečtěte si o upgradu verze nebo konfigurace clusteru Azure Service Fabric – nastavení režimu aktualizace clusteru, upgrade certifikátů, přidání portů aplikace, provádění oprav operačního systému a to, co můžete očekávat při provádění upgradu.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: d92ac90e0e41d534231bafbe991a05764dbee07d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82789551"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Upgrade a aktualizace clusteru Azure Service Fabric

Pro jakýkoliv moderní systém je pro zajištění dlouhodobé úspěšnosti produktu navržený návrh pro zajištění náročnosti. Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně ho spravuje Microsoft. Tento článek popisuje, co je spravované automaticky a co můžete nakonfigurovat sami.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze prostředků infrastruktury, která běží na vašem clusteru

Ujistěte se, že cluster vždy používá [podporovanou verzi prostředků infrastruktury](service-fabric-versions.md). Pokaždé, když společnost Microsoft oznamuje vydání nové verze Service Fabric, je předchozí verze označena pro konec podpory po dobu minimálně 60 dní od tohoto data. Nové verze jsou oznámeny na [blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dnů před vypršením platnosti vydaných verzí clusteru se vygeneruje událost stavu, která převede váš cluster do stavu s varováním. Cluster zůstane ve stavu varování, dokud neprovedete upgrade na podporovanou verzi prostředků infrastruktury.

Cluster můžete nastavit tak, aby přijímal automatické upgrady prostředků infrastruktury, když jsou vydané společností Microsoft, nebo můžete vybrat podporovanou verzi prostředků infrastruktury, na které má být cluster zapnutý.  Pokud se chcete dozvědět víc, přečtěte si téma [Upgrade verze Service Fabric vašeho clusteru](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Chování upgradu prostředků infrastruktury během automatických upgradů

Společnost Microsoft udržuje kód a konfiguraci prostředků infrastruktury, které běží v clusteru Azure. Automatické monitorované upgrady na software provádíme v závislosti na potřebách. Tyto upgrady můžou být kód, konfigurace nebo obojí. Pokud chcete zajistit, aby vaše aplikace neutrpěla žádný dopad nebo minimální dopad vyplývající z těchto upgradů, provedou se upgrady v následujících fázích:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fáze 1: upgrade se provádí pomocí všech zásad stavu clusteru.

V průběhu této fáze budou upgrady dál používat jednu doménu upgradu a aplikace, které byly spuštěny v clusteru, budou i nadále běžet bez výpadků. Zásady stavu clusteru (pro stav uzlu a stav aplikace) jsou v průběhu upgradu dodrženy.

Pokud se zásady stavu clusteru nesplní, upgrade se vrátí zpátky a vlastníkovi předplatného se pošle e-mail. E-mail obsahuje následující informace:

* Oznámení, že jsme museli vrátit upgrade clusteru zpět.
* Navrhované nápravné akce, pokud existují.
* Počet dní (*n*), dokud neprovedeme fázi 2.

Zkusíme provést stejný upgrade několikrát pro případ, že se z důvodů infrastruktury nezdařily žádné upgrady. Po *n* dnech od data odeslání e-mailu budeme pokračovat ke fázi 2.

Pokud jsou splněné zásady stavu clusteru, upgrade se považuje za úspěšný a označený jako úplný. K tomu může dojít během prvotního upgradu nebo při každém spuštění upgradu v této fázi. Neexistují žádné e-mailové potvrzení úspěšného spuštění. Je tomu tak zabránit odeslání příliš velkého počtu e-mailů. příjem e-mailu by se měl zobrazit jako výjimka do normálního. Očekáváme, že většina upgradů clusteru bude úspěšná, aniž by to ovlivnilo dostupnost vaší aplikace.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fáze 2: upgrade se provádí jenom pomocí výchozích zásad stavu.

Zásady stavu v této fázi se nastavují tak, aby počet aplikací, které byly na začátku upgradu v pořádku, zůstal po dobu trvání procesu upgradu stejný. Stejně jako ve fázi 1 budou upgrady fáze 2 pokračovat v jednom upgradu domény a aplikace spuštěné v clusteru budou i nadále běžet bez jakéhokoli výpadku. Zásady stavu clusteru (kombinace stavu uzlu a stavu všech aplikací spuštěných v clusteru) se řídí po dobu trvání upgradu.

Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět. Pak se pošle e-mailem vlastníkovi předplatného. E-mail obsahuje následující informace:

* Oznámení, že jsme museli vrátit upgrade clusteru zpět.
* Navrhované nápravné akce, pokud existují.
* Počet dní (n) až do provedení fáze 3.

Zkusíme provést stejný upgrade několikrát pro případ, že se z důvodů infrastruktury nezdařily žádné upgrady. E-mailem s připomenutím se pošle pár dní před n dny. Po n dnech od data odeslání e-mailu budeme pokračovat ke fázi 3. E-maily, které vám pošleme ve fázi 2, se musí považovat za vážně a musí se provést nápravná opatření.

Pokud jsou splněné zásady stavu clusteru, upgrade se považuje za úspěšný a označený jako úplný. K tomu může dojít během prvotního upgradu nebo při každém spuštění upgradu v této fázi. Neexistují žádné e-mailové potvrzení úspěšného spuštění.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fáze 3: upgrade se provádí pomocí agresivních zásad stavu.

Tyto zásady stavu v této fázi jsou zaměřeny na dokončení upgradu, nikoli na stav aplikací. V této fázi skončí několik upgradů clusteru. Pokud se váš cluster dostane do této fáze, je velmi pravděpodobné, že se vaše aplikace stane v pořádku nebo ztratí dostupnost.

Podobně jako v ostatních dvou fázích upgrady fáze 3 v jednom okamžiku překročí jednu upgradovací doménu.

Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět. Zkusíme provést stejný upgrade několikrát pro případ, že se z důvodů infrastruktury nezdařily žádné upgrady. Potom je cluster připnutý, takže už nebude přijímat podporu a/nebo upgrady.

E-mail s těmito informacemi se pošle vlastníkovi předplatného, a to i v případě nápravných akcí. Neočekáváme, že se žádné clustery dostanou do stavu, ve kterém se fáze 3 nezdařila.

Pokud jsou splněné zásady stavu clusteru, upgrade se považuje za úspěšný a označený jako úplný. K tomu může dojít během prvotního upgradu nebo při každém spuštění upgradu v této fázi. Neexistují žádné e-mailové potvrzení úspěšného spuštění.

## <a name="manage-certificates"></a>Správa certifikátů

Service Fabric používá [certifikáty serveru X. 509](service-fabric-cluster-security.md) , které zadáte při vytváření clusteru pro zabezpečení komunikace mezi uzly clusteru a ověřování klientů. Certifikáty pro cluster a klienta můžete přidat, aktualizovat nebo odstranit v [Azure Portal](https://portal.azure.com) nebo pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.  Další informace najdete v článku [Přidání nebo odebrání certifikátů](service-fabric-cluster-security-update-certs-azure.md) .

## <a name="open-application-ports"></a>Otevřít porty aplikace

Můžete změnit porty aplikace změnou vlastností prostředku Load Balancer, které jsou přidruženy k typu uzlu. Můžete použít Azure Portal, nebo můžete použít PowerShell nebo Azure CLI. Další informace najdete v tématu [otevření portů aplikací pro cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definovat vlastnosti uzlu

Někdy možná budete chtít zajistit, aby některé úlohy běžely jenom na určitých typech uzlů v clusteru. Některé úlohy můžou například vyžadovat GPU nebo SSD, zatímco jiné nemusí. Pro každý typ uzlu v clusteru můžete přidat vlastní vlastnosti uzlu do uzlů clusteru. Omezení umístění jsou příkazy připojené k jednotlivým službám, které jsou vybrané pro jednu nebo více vlastností uzlu. Omezení umístění definují, kde by měly služby běžet.

Podrobnosti o použití omezení umístění, vlastnostech uzlů a způsobu jejich definování, čtení [vlastností uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Přidat metriky kapacity

Pro každý typ uzlu můžete přidat vlastní metriky kapacity, které chcete použít ve svých aplikacích, abyste nahlásili zatížení. Podrobnosti o použití metriky kapacity k načtení sestav najdete v tématu Service Fabric clusteru Správce prostředků dokumentu [popisujícího cluster](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Nastavení zásad stavu pro automatické upgrady

Pro upgrade prostředků infrastruktury můžete zadat vlastní zásady stavu. Pokud jste cluster nastavili na automatické upgrady prostředků infrastruktury, pak se tyto zásady uplatní na fázi 1 pro automatické upgrady prostředků infrastruktury.
Pokud jste cluster nastavili pro ruční upgrady prostředků infrastruktury, pak se tyto zásady uplatní pokaždé, když vyberete novou verzi, která aktivuje upgrade prostředků infrastruktury ve vašem clusteru. Pokud zásady nepřepisujete, použijí se výchozí hodnoty.

Můžete zadat vlastní zásady stavu nebo zkontrolovat aktuální nastavení v okně "upgrade prostředků infrastruktury" výběrem možnosti Upřesnit nastavení upgradu. Přečtěte si následující obrázek o postupu.

![Správa vlastních zásad stavu][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Přizpůsobení nastavení prostředků infrastruktury pro cluster

V clusteru je možné přizpůsobit mnoho různých nastavení konfigurace, jako je například úroveň spolehlivosti clusteru a vlastností uzlu. Další informace najdete v tématu [Service Fabric nastavení prostředků infrastruktury clusteru](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Oprava operačního systému v uzlech clusteru

Aplikace orchestrace oprav (POA) je Service Fabric aplikace, která automatizuje opravy operačního systému v clusteru Service Fabric bez výpadků. [Aplikace orchestrace oprav pro Windows](service-fabric-patch-orchestration-application.md) se dá nasadit do clusteru a instalovat opravy do Orchestrace a zároveň přitom zachovat dostupné služby.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak přizpůsobit některá [nastavení prostředků infrastruktury Service Fabric pro clustery](service-fabric-cluster-fabric-settings.md)
* Přečtěte si, jak [škálovat cluster na úrovni a ven](service-fabric-cluster-scale-in-out.md) .
* Další informace o [upgradech aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
