---
title: Upgrade clusterů Azure Service Fabric
description: Další informace o možnostech aktualizace clusteru Azure Service Fabric
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731104"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Upgrade a aktualizace clusterů Azure Service Fabric

Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně ho spravuje Microsoft. Tento článek popisuje možnosti, kdy a jak aktualizovat cluster Azure Service Fabric.

## <a name="automatic-versus-manual-upgrades"></a>Automatické porovnání ručních upgradů

Je důležité zajistit, aby v clusteru Service Fabric vždy běžela [podporovaná verze modulu runtime](service-fabric-versions.md). Pokaždé, když společnost Microsoft oznamuje vydání nové verze Service Fabric, je předchozí verze označena pro *konec podpory* po dobu minimálně 60 dní od tohoto data. Nové verze jsou oznámeny na [blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

Čtrnáct dní před vypršením platnosti vydaných verzí clusteru je vygenerována událost stavu, která převede váš cluster do stavu *varování* . Cluster zůstane ve stavu varování, dokud neprovedete upgrade na podporovanou verzi modulu runtime.

Cluster můžete nastavit tak, aby přijímal automatické upgrady Service Fabric, když jsou vydávány společností Microsoft, nebo můžete ručně vybrat ze seznamu aktuálně podporovaných verzí. Tyto možnosti jsou k dispozici v části **upgrade prostředků infrastruktury** v prostředku Service Fabricho clusteru.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="V části &quot;upgrady prostředků infrastruktury&quot; v prostředku clusteru v Azure Portal vyberte Automatické nebo ruční upgrady.":::

Můžete také nastavit režim upgradu clusteru a vybrat verzi modulu runtime [pomocí šablony Správce prostředků](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Automatické upgrady jsou doporučeným režimem upgradu, protože tato možnost zajistí, že váš cluster zůstane v podporovaném stavu a přináší výhody z nejnovějších oprav a funkcí a zároveň umožňuje naplánovat aktualizace způsobem, který je nejmenším dopadem na vaše úlohy pomocí strategie [nasazení Wave](#wave-deployment-for-automatic-upgrades) .

## <a name="wave-deployment-for-automatic-upgrades"></a>Nasazení vlnovkou pro automatické upgrady

S nasazením Wave můžete minimalizovat přerušení upgradu na svůj cluster tak, že v závislosti na vašem zatížení vyberete úroveň zralosti upgradu. Můžete například nastavit   ->    ->  kanál nasazení *provozní* vlny pro různé Service Fabric clustery, abyste mohli otestovat kompatibilitu s upgradem modulu runtime předtím, než ho použijete na produkční úlohy.

Pokud se chcete přihlásit k nasazení Wave, zadejte jednu z následujících hodnot Wave pro váš cluster (v šabloně nasazení):

* **Wave 0**: clustery se aktualizují hned po vydání nového buildu Service Fabric. Určeno pro testovací a vývojové clustery.
* **Wave 1**: clustery se aktualizují po jednom týdnu (sedm dní) po uvolnění nového sestavení. Určeno pro předem připravené a přípravné clustery.
* **Wave 2**: clustery se aktualizují za dva týdny (14 dní) po uvolnění nového sestavení. Určeno pro produkční clustery.

Můžete se zaregistrovat k e-mailovým oznámením s odkazy na další pomoc v případě, že dojde k chybě upgradu clusteru. Pokud chcete začít, podívejte se na téma [nasazení Wave pro automatické upgrady](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) .

## <a name="phases-of-automatic-upgrade"></a>Fáze automatického upgradu

Společnost Microsoft udržuje kód Service Fabric runtime a konfiguraci, která běží v clusteru Azure. V závislosti na tom, co je potřeba, provádíme automaticky monitorované upgrady softwaru. Tyto upgrady můžou být kód, konfigurace nebo obojí. Chcete-li minimalizovat dopad těchto upgradů na vaše aplikace, jsou provedeny v následujících fázích:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fáze 1: upgrade se provádí pomocí všech zásad stavu clusteru.

V průběhu této fáze budou upgrady dál používat jednu doménu upgradu a aplikace, které byly spuštěny v clusteru, budou i nadále běžet bez výpadků. Zásady stavu clusteru (pro stav uzlu a stav aplikace) jsou v průběhu upgradu dodrženy.

Pokud se zásady stavu clusteru nesplní, upgrade se vrátí zpátky a vlastníkovi předplatného se pošle e-mail. E-mail obsahuje následující informace:

* Oznámení, že jsme museli vrátit upgrade clusteru zpět.
* Navrhované nápravné akce, pokud existují.
* Počet dní (*n*), dokud neprovedeme fázi 2.

Zkusíme provést stejný upgrade několikrát pro případ, že se z důvodů infrastruktury nezdařily žádné upgrady. Po *n* dnech od data odeslání e-mailu budeme pokračovat do fáze 2.

Pokud jsou splněné zásady stavu clusteru, upgrade se považuje za úspěšný a označený jako úplný. K této situaci může dojít při počátečním upgradu nebo při jakémkoli spuštění upgradu v této fázi. Nebylo provedeno žádné e-mailové potvrzení úspěšného spuštění, aby nedocházelo k posílání nadměrných e-mailů Příjem e-mailu indikuje výjimku pro normální operace. Očekáváme, že většina upgradů clusteru bude úspěšná, aniž by to ovlivnilo dostupnost vaší aplikace.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fáze 2: upgrade se provádí jenom pomocí výchozích zásad stavu.

Zásady stavu v této fázi se nastavují tak, aby počet aplikací, které byly v pořádku na začátku upgradu, zůstal stejný během procesu upgradu. Stejně jako ve fázi 1 budou upgrady fáze 2 pokračovat v jednom upgradu domény a aplikace spuštěné v clusteru budou i nadále běžet bez jakéhokoli výpadku. Zásady stavu clusteru (kombinace stavu uzlu a stavu všech aplikací spuštěných v clusteru) jsou v průběhu upgradu dodrženy.

Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět. Pak se pošle e-mailem vlastníkovi předplatného. E-mail obsahuje následující informace:

* Oznámení, že jsme museli vrátit upgrade clusteru zpět.
* Navrhované nápravné akce, pokud existují.
* Počet dní (n) až do provedení fáze 3.

Zkusíme provést stejný upgrade několikrát pro případ, že se z důvodů infrastruktury nezdařily žádné upgrady. E-mailem s připomenutím se pošle pár dní před n dny. Po n dnech od data odeslání e-mailu budeme pokračovat ke fázi 3. E-maily, které vám pošleme ve fázi 2, se musí považovat za vážně a musí se provést nápravná opatření.

Pokud jsou splněné zásady stavu clusteru, upgrade se považuje za úspěšný a označený jako úplný. K tomu může dojít během prvotního upgradu nebo při každém spuštění upgradu v této fázi. Neexistují žádné e-mailové potvrzení úspěšného spuštění.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fáze 3: upgrade se provádí pomocí agresivních zásad stavu.

Tyto zásady stavu v této fázi jsou zaměřeny na dokončení upgradu, nikoli na stav aplikací. V této fázi skončí několik upgradů clusteru. Pokud se váš cluster dostane do této fáze, je velmi pravděpodobné, že vaše aplikace přestane být v pořádku nebo ztratí dostupnost.

Podobně jako v ostatních dvou fázích upgrady fáze 3 v jednom okamžiku překročí jednu upgradovací doménu.

Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět. Zkusíme provést stejný upgrade několikrát pro případ, že se z důvodů infrastruktury nezdařily žádné upgrady. Potom je cluster připnutý, takže už nebude přijímat podporu a/nebo upgrady.

E-mail s těmito informacemi se pošle vlastníkovi předplatného, a to i v případě nápravných akcí. Neočekáváme, že se žádné clustery dostanou do stavu, ve kterém se fáze 3 nezdařila.

Pokud jsou splněné zásady stavu clusteru, upgrade se považuje za úspěšný a označený jako úplný. K tomu může dojít během prvotního upgradu nebo při každém spuštění upgradu v této fázi. Neexistují žádné e-mailové potvrzení úspěšného spuštění.

## <a name="custom-policies-for-manual-upgrades"></a>Vlastní zásady pro ruční upgrady

Můžete zadat vlastní zásady pro ruční upgrady clusteru. Tyto zásady se uplatní pokaždé, když vyberete novou verzi modulu runtime, která aktivuje systém za účelem zahájení upgradu clusteru. Pokud zásady nepřepisujete, použijí se výchozí hodnoty. Další informace najdete v tématu [Nastavení vlastních zásad pro ruční upgrady](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades).

## <a name="other-cluster-updates"></a>Další aktualizace clusteru

Mimo upgrade modulu runtime je k dispozici řada dalších akcí, které je třeba provést, aby se cluster udržoval v aktuálním stavu, včetně následujících:

### <a name="managing-certificates"></a>Správa certifikátů

Service Fabric používá [certifikáty serveru X. 509](service-fabric-cluster-security.md) , které zadáte při vytváření clusteru pro zabezpečení komunikace mezi uzly clusteru a ověřování klientů. Certifikáty pro cluster a klienta můžete přidat, aktualizovat nebo odstranit v [Azure Portal](https://portal.azure.com) nebo pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.  Další informace najdete v článku [Přidání nebo odebrání certifikátů](service-fabric-cluster-security-update-certs-azure.md) .

### <a name="opening-application-ports"></a>Otevírání portů aplikace

Můžete změnit porty aplikace změnou vlastností prostředku Load Balancer, které jsou přidruženy k typu uzlu. Můžete použít Azure Portal, nebo můžete použít PowerShell nebo Azure CLI. Další informace najdete v tématu [otevření portů aplikací pro cluster](create-load-balancer-rule.md).

### <a name="defining-node-properties"></a>Definování vlastností uzlu

Někdy možná budete chtít zajistit, aby některé úlohy běžely jenom na určitých typech uzlů v clusteru. Některé úlohy můžou například vyžadovat GPU nebo SSD, zatímco jiné nemusí. Pro každý typ uzlu v clusteru můžete přidat vlastní vlastnosti uzlu do uzlů clusteru. Omezení umístění jsou příkazy připojené k jednotlivým službám, které jsou vybrané pro jednu nebo více vlastností uzlu. Omezení umístění definují, kde by měly služby běžet.

Podrobnosti o použití omezení umístění, vlastnostech uzlů a způsobu jejich definování, čtení [vlastností uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

### <a name="adding-capacity-metrics"></a>Přidávání metrik kapacity

Pro každý typ uzlu můžete přidat vlastní metriky kapacity, které chcete použít ve svých aplikacích, abyste nahlásili zatížení. Podrobnosti o použití metriky kapacity k načtení sestav najdete v tématu Service Fabric clusteru Správce prostředků dokumentu [popisujícího cluster](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md).

### <a name="customizing-settings-for-your-cluster"></a>Přizpůsobení nastavení clusteru

V clusteru je možné přizpůsobit mnoho různých nastavení konfigurace, jako je například úroveň spolehlivosti clusteru a vlastností uzlu. Další informace najdete v tématu [Service Fabric nastavení prostředků infrastruktury clusteru](service-fabric-cluster-fabric-settings.md).

### <a name="upgrading-os-images-for-cluster-nodes"></a>Upgrade imagí operačního systému pro uzly clusteru

Osvědčeným postupem je povolení automatických upgradů bitových kopií operačního systému pro uzly Service Fabric clusteru. K tomu je potřeba provést několik požadavků a kroků clusteru. Další možností je použití aplikace Orchestration Application (POA, Service Fabric aplikace, která automatizuje opravy operačního systému v clusteru Service Fabric bez výpadků. Další informace o těchto možnostech najdete v tématu [oprava operačního systému Windows v clusteru Service Fabric](service-fabric-patch-orchestration-application.md).

## <a name="next-steps"></a>Další kroky

* [Správa upgradů Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Přizpůsobení [nastavení Service Fabric clusteru](service-fabric-cluster-fabric-settings.md)
* [Horizontální navýšení nebo navýšení kapacity clusteru](service-fabric-cluster-scale-in-out.md)
* Další informace o [upgradech aplikací](service-fabric-application-upgrade.md)
