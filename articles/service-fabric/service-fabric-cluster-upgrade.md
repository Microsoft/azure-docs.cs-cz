---
title: Upgrade clusteru služby Azure Service Fabric | Microsoft Docs
description: Upgrade Service Fabric kód nebo konfigurace používající cluster Service Fabric, včetně nastavení režimu aktualizace clusteru upgrade certifikáty, přidání aplikace porty, provádění oprav operačního systému, a tak dále. Co můžete očekávat, když se provádí upgrady?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 22dae85c4125c54003f65aba3ef112ffcec9b76d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Upgrade clusteru služby Azure Service Fabric
> [!div class="op_single_selector"]
> * [Azure clusteru](service-fabric-cluster-upgrade.md)
> * [Samostatné clusteru](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Pro libovolný systém moderní návrhu pro zdokonalovány je klíčem k dosažení dlouhodobý úspěch vašeho produktu. Cluster služby Azure Service Fabric je na prostředek, který vlastníte, ale je částečně spravováno společností Microsoft. Tento článek popisuje, co je spravováno automaticky a co můžete nakonfigurovat sami.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze fabric, která běží na clusteru
Můžete nastavit na automatické fabric aktualizace vydané společností Microsoft nebo můžete vybrat verzi podporované fabric, že chcete clusteru na clusteru.

To provedete nastavením konfigurace clusteru "upgradeMode" na portálu nebo pomocí Správce prostředků v době vytvoření nebo později na clusteru s podporou za provozu 

> [!NOTE]
> Ujistěte se, že zachovat clusteru vždy používá verzi podporovanou prostředků infrastruktury. Jak a kdy jsme oznamujeme vydání nové verze služby infrastruktury, předchozí verze budou označena k ukončení podpory po 60 dnů od tohoto dne. nové verze není zmínka [na blogu týmu služby fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nové verze je k dispozici, pak vybrat. 
> 
> 

14 dnů před vypršení platnosti verze, které váš clusteru je spuštěn, událost stavu se vygeneruje, umístí clusteru do stavu upozornění. Cluster zůstane ve stavu upozornění, dokud neprovedete upgrade na verzi, podporované prostředků infrastruktury.

### <a name="setting-the-upgrade-mode-via-portal"></a>Nastavení režimu upgradu prostřednictvím portálu
Clusteru můžete nastavit na automatické nebo ruční při vytváření clusteru.

![Create_Manualmode][Create_Manualmode]

Clusteru můžete nastavit na automatické nebo ruční v clusteru s podporou za provozu, pomocí možnosti Správa. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgrade na novou verzi v clusteru, který je nastavený na ruční režim přes portál.
Pokud chcete upgradovat na novou verzi, všechny, které musíte udělat je z rozevíracího seznamu vyberte verzi k dispozici a uložte. Upgrade pro Fabric získá spuštěna automaticky. Zásady stavu clusteru (kombinaci uzlu stav a stav všechny aplikace běžící v clusteru) jsou, kterého se během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade je vrácena zpět. Projděte dolů tento dokument Další informace o tom, jak nastavit tyto zásady vlastní stavu. 

Po opravě problémy, jejichž výsledkem vrácení zpět, budete muset znovu zahájit upgrade podle následujících kroků stejná jako před.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Nastavení režimu upgradu pomocí šablony Resource Manageru
Přidejte definici zdrojů Microsoft.ServiceFabric/clusters konfiguraci "upgradeMode" a "clusterCodeVersion" nastavena na jednu z verzí podporované prostředků infrastruktury, jak je uvedeno níže a pak nasazení šablony. Platné hodnoty pro "upgradeMode" jsou "Ruční" nebo "Automatické"

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgrade na novou verzi v clusteru, který je nastavený na ruční režim pomocí šablony Resource Manageru.
Až bude cluster v ručním režimu, upgrade na novou verzi, změňte "clusterCodeVersion" na podporovanou verzi a nasadit. Nasazení šablony, zejména kopance o upgrade pro Fabric získá spuštěna automaticky. Zásady stavu clusteru (kombinaci uzlu stav a stav všechny aplikace běžící v clusteru) jsou, kterého se během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade je vrácena zpět. Projděte dolů tento dokument Další informace o tom, jak nastavit tyto zásady vlastní stavu. 

Po opravě problémy, jejichž výsledkem vrácení zpět, budete muset znovu zahájit upgrade podle následujících kroků stejná jako před.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Získejte seznam všech dostupných verze pro všechna prostředí pro dané předplatné
Spusťte následující příkaz a měli byste obdržet výstup podobně jako tento.

"supportExpiryUtc" informuje vaší Pokud danou verzi vyprší nebo vypršela platnost. Nejnovější verze nemá platné datum - má hodnotu "9999-12-31T23:59:59.9999999", což právě znamená, že datum vypršení platnosti ještě není nainstalovaný.

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

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Fabric upgradu chování, pokud cluster upgradovat režimu je automatický
Společnost Microsoft udržuje kód prostředků infrastruktury a konfigurace, která běží v clusteru služby Azure. Můžeme provést automatické upgrady monitorovaných softwaru podle potřeby. Tyto upgrady může být kódu, konfigurace nebo obojí. Abyste měli jistotu, že vaše aplikace vyskytne žádný dopad nebo minimální dopad kvůli tyto upgrady, jsme upgrady provádět v následujících fází:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fáze 1: Upgrade se provádí pomocí všechny zásady stavu clusteru
V průběhu této fáze upgrady pokračovat jednu upgradovací doménu najednou a aplikace, které byly spuštěny v clusteru dál běžet bez odstávky. Zásady stavu clusteru (kombinaci uzlu stav a stav všechny aplikace běžící v clusteru) jsou, kterého se během upgradu.

Pokud zásady stavu clusteru nejsou splněny, upgrade je vrácena zpět. E-mailu se pak odešlou vlastníka předplatného. E-mailu obsahuje následující informace:

* Oznámení, že jsme museli vrátit zpět na upgrade clusteru.
* Navrhované nápravné akce, pokud existuje.
* Počet dní (ne), dokud jsme provést fáze 2.

Pokusíme se provést stejný upgrade několik vícekrát v případě, že jakéhokoli upgradu se nezdařilo z důvodů infrastruktury. Po n dní od data, kdy byla odeslána e-mailu budeme pokračovat fáze 2.

Pokud jsou splněny zásady stavu clusteru, je upgrade považuje za úspěšnou a označeny jako dokončené. Může dojít během počáteční upgradu nebo některého z opakování upgradu v této fázi. Neexistuje žádné potvrzení e-mailu úspěšně spustit. Toto je vyhnout odesílání, že jste příliš mnoho e-mailů – příjem e-mailu měla by se zobrazit jako výjimku do normální. Očekáváme, že většina upgrady clusteru úspěšné bez dopadu na dostupnost vaší aplikace.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fáze 2: Upgrade se provádí pomocí výchozího stavu pouze zásady
Zásady stavu v této fázi jsou nastavené tak, že počet aplikací, které byly na začátku upgradu v pořádku, zůstane stejný po dobu trvání procesu upgradu. Jako fáze 1 upgrady fáze 2 pokračovat jednu upgradovací doménu najednou a aplikace, které byly spuštěny v clusteru dál běžet bez odstávky. Zásady stavu clusteru (kombinaci uzlu stav a stav všechny aplikace běžící v clusteru) jsou zachovány po dobu trvání upgradu.

Pokud platí zásady stavu clusteru nejsou splněny, upgrade je vrácena zpět. E-mailu se pak odešlou vlastníka předplatného. E-mailu obsahuje následující informace:

* Oznámení, že jsme museli vrátit zpět na upgrade clusteru.
* Navrhované nápravné akce, pokud existuje.
* Počet dní (ne), dokud jsme provést fáze 3.

Pokusíme se provést stejný upgrade několik vícekrát v případě, že jakéhokoli upgradu se nezdařilo z důvodů infrastruktury. Připomenutí e-mail je odeslán během několika dní, než n dní jsou. Po n dní od data, kdy byla odeslána e-mailu budeme pokračovat fáze 3. E-mailů, které vám můžeme poslat v fáze 2 bude nutno vážně a musí být přijata nápravné akce.

Pokud jsou splněny zásady stavu clusteru, je upgrade považuje za úspěšnou a označeny jako dokončené. Může dojít během počáteční upgradu nebo některého z opakování upgradu v této fázi. Neexistuje žádné potvrzení e-mailu úspěšně spustit.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fáze 3: Upgrade se provádí na základě zásad agresivní stavu
Tyto zásady stavu v této fázi jsou s ohledem na dokončení upgradu, nikoli stavu aplikace. V této fázi skončili jen několik upgradů clusteru. Pokud váš cluster získá do této fáze, je velmi pravděpodobné, že vaše aplikace se změní na není v pořádku nebo ztrátu dostupnosti.

Podobně jako u jiných dvě fáze, fáze 3 upgrady pokračovat jednu upgradovací doménu najednou.

Pokud zásady stavu clusteru nejsou splněny, upgrade je vrácena zpět. Pokusíme se provést stejný upgrade několik vícekrát v případě, že jakéhokoli upgradu se nezdařilo z důvodů infrastruktury. Potom je ukotvena clusteru, tak, aby se už nebude podporu nebo upgrady.

E-mail s tyto informace je odeslán vlastník předplatného, společně s nápravné akce. Není Očekáváme, že všechny clustery získat do stavu, kde se nezdařilo fáze 3.

Pokud jsou splněny zásady stavu clusteru, je upgrade považuje za úspěšnou a označeny jako dokončené. Může dojít během počáteční upgradu nebo některého z opakování upgradu v této fázi. Neexistuje žádné potvrzení e-mailu úspěšně spustit.

## <a name="cluster-configurations-that-you-control"></a>Konfigurace clusteru, které řídíte
Kromě možnost nastavit cluster upgradovat režimu, tady jsou konfigurace, které můžete změnit na cluster za provozu.

### <a name="certificates"></a>Certifikáty
Můžete přidat nové nebo snadno odstranit certifikáty pro cluster a klienta přes portál. Odkazovat na [tento dokument podrobné pokyny](service-fabric-cluster-security-update-certs-azure.md)

![Snímek obrazovky zobrazující kryptografické otisky certifikátu na portálu Azure.][CertificateUpgrade]

### <a name="application-ports"></a>Porty aplikace
Porty aplikace můžete změnit změnou vlastnosti prostředků nástroj pro vyrovnávání zatížení, které jsou přidruženy k typu uzlu. Můžete použít na portálu, nebo můžete použít Správce prostředků PowerShell přímo.

Otevřete nový port na všech virtuálních počítačích v typ uzlu, postupujte takto:

1. Přidáte nový test odpovídající služba Vyrovnávání zatížení.
   
    Pokud jste nasadili cluster pomocí portálu, jsou nástroje pro vyrovnávání zatížení s názvem "LB-název skupiny prostředků – NodeTypename", jednu pro každý typ uzlu. Vzhledem k tomu, že jsou pouze ve skupině prostředků jedinečné názvy nástroje pro vyrovnávání zatížení, je nejlepší Pokud vyhledávání v rámci určité skupiny zdrojů.
   
    ![Snímek obrazovky, který ukazuje sondu přidání do Vyrovnávání zatížení na portálu.][AddingProbes]
2. Přidáte nové pravidlo Vyrovnávání zatížení.
   
    Přidáte nové pravidlo Vyrovnávání zatížení, stejné pomocí testu, který jste vytvořili v předchozím kroku.
   
    ![Přidání nové pravidlo Vyrovnávání zatížení na portálu.][AddingLBRules]

### <a name="placement-properties"></a>Vlastnosti umístění
Pro každý typ uzlu můžete přidat vlastní umístění vlastnosti, které chcete používat ve svých aplikacích. Typ uzlu je ve výchozím nastavení, kterou můžete použít bez přidání explicitně.

> [!NOTE]
> Podrobnosti o použití omezení umístění, vlastnosti uzlu a jak se definovat informace naleznete v sekci "Umístění omezení a vlastnosti uzlu" v dokumentu správce prostředků clusteru Service Fabric na [popisující clusteru](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Metriky kapacity
Pro každý typ uzlu můžete přidat vlastní kapacity metriky, které chcete použít v aplikacích pro zatížení sestavy. Podrobnosti o použití metriky kapacity pro zatížení sestavy, získáte v dokumentech správce prostředků clusteru Service Fabric na [popisující vaše clusteru](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Upgrade nastavení prostředků infrastruktury – zásady stavu
Můžete zadat, že stav vlastní zásady pro upgrade pro fabric. Pokud nastavíte clusteru na fabric automatické upgrady získat tyto zásady použít pro fáze 1 upgrady automatické prostředků infrastruktury.
Pokud jste nastavili clusteru pro ruční fabric upgrady, získat tyto zásady použít pokaždé, když vyberete novou verzi spouštění systému ji upgrade pro fabric v clusteru. Pokud není přepsat zásady, budou použity výchozí hodnoty.

Můžete určit zásady vlastní stavu nebo zkontrolujte aktuální nastavení v okně "upgrade pro fabric" tak, že vyberete pokročilé nastavení upgradu. Přečtěte si o tom, jak na následujícím obrázku. 

![Správa vlastní stav zásad][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Přizpůsobení nastavení prostředků infrastruktury pro váš cluster
Odkazovat na [nastavení prostředků infrastruktury clusteru prostředků infrastruktury služby](service-fabric-cluster-fabric-settings.md) na co a jak je můžete přizpůsobit.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Opravy operačního systému na virtuální počítače, které tvoří clusteru
Odkazovat na [použití opravy Orchestration](service-fabric-patch-orchestration-application.md) které mohl být nasazen v clusteru orchestrated způsobem, zachovat dostupnost služeb vždy instalaci oprav ze služby Windows Update. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Upgrade operačního systému na virtuální počítače, které tvoří clusteru
Pokud upgradujete bitovou kopii operačního systému na virtuální počítače clusteru, je nutné se jeden virtuální počítač najednou. Jste zodpovědní pro tento upgrade – v současné době není žádné automation pro tento.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak přizpůsobit některé [nastavení prostředků infrastruktury clusteru prostředků infrastruktury služby](service-fabric-cluster-fabric-settings.md)
* Zjistěte, jak [škálování vašeho clusteru a odhlášení](service-fabric-cluster-scale-up-down.md)
* Další informace o [upgradů aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
