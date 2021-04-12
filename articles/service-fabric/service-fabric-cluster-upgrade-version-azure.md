---
title: Správa upgradů Service Fabric clusteru
description: Správa doby a způsobu aktualizace modulu runtime clusteru Service Fabric
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731153"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Správa upgradů Service Fabric clusteru

Cluster Azure Service Fabric je prostředek, který vlastníte, ale částečně ho spravuje Microsoft. Tady je postup, jak spravovat, kdy a jak Microsoft aktualizuje váš cluster Azure Service Fabric.

Další informace o konceptech a procesech upgradu clusteru najdete v tématu [upgrade a aktualizace clusterů Azure Service Fabric](service-fabric-cluster-upgrade.md) .

## <a name="set-upgrade-mode"></a>Nastavit režim upgradu

Cluster můžete nastavit tak, aby přijímal automatické upgrady Service Fabric, když jsou vydávány společností Microsoft, nebo můžete ručně vybrat ze seznamu aktuálně podporovaných verzí nastavením režimu upgradu pro váš cluster. To lze provést buď prostřednictvím ovládacího prvku *režim upgradu prostředků infrastruktury* v Azure Portal nebo `upgradeMode` nastavením v šabloně nasazení clusteru.

### <a name="azure-portal"></a>portál Azure

Pomocí Azure Portal se při vytváření nového clusteru Service Fabric rozhodnete mezi automatickými nebo ručními upgrady.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Volba automatického nebo ručního upgradu při vytváření nového clusteru v Azure Portal z možností upřesnění":::

Můžete také přepínat mezi automatickými nebo ručními upgrady z oddílu s **upgrady prostředků infrastruktury** v existujícím prostředku clusteru.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="V části &quot;upgrady prostředků infrastruktury&quot; v prostředku clusteru v Azure Portal vyberte Automatické nebo ruční upgrady.":::

### <a name="manual-upgrades-with-azure-portal"></a>Ruční upgrady pomocí Azure Portal

Když vyberete možnost ruční upgrade, vše, co je potřeba k zahájení upgradu, se vybere z rozevíracího seznamu dostupné verze a pak se *uloží*. Odtud se upgrade clusteru hned vypíná.

[Zásady stavu clusteru](#custom-policies-for-manual-upgrades) (kombinace stavu uzlu a stavu všech aplikací spuštěných v clusteru) jsou v průběhu upgradu dodrženy. Pokud nejsou splněné zásady stavu clusteru, upgrade se vrátí zpět.

Jakmile opravíte problémy, které vedly k vrácení zpět, budete muset znovu zahájit upgrade, a to pomocí stejných kroků jako v tomto postupu.

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Chcete-li změnit režim upgradu clusteru pomocí šablony Správce prostředků, zadejte buď možnost *Automatické* , nebo *Ruční* pro  `upgradeMode` vlastnost definice *prostředků Microsoft. ServiceFabric/Clusters* . Pokud zvolíte ruční upgrady, nastavte také `clusterCodeVersion` na aktuálně [podporovanou verzi prostředků infrastruktury](#query-for-supported-cluster-versions).

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="Snímek obrazovky zobrazuje šablonu, která je ve formátu prostého textu odrážka, aby odrážela strukturu. Vlastnosti ' clusterCodeVersion ' a ' upgradeMode ' jsou zvýrazněny.":::

Po úspěšném nasazení šablony se použijí změny v režimu upgradu clusteru. Pokud je váš cluster v ručním režimu, upgrade clusteru se automaticky aktivuje.

[Zásady stavu clusteru](#custom-policies-for-manual-upgrades) (kombinace stavu uzlu a stavu všech aplikací spuštěných v clusteru) jsou v průběhu upgradu dodrženy. Pokud nejsou splněné zásady stavu clusteru, upgrade se vrátí zpět.

Jakmile opravíte problémy, které vedly k vrácení zpět, budete muset znovu zahájit upgrade, a to pomocí stejných kroků jako v tomto postupu.

## <a name="wave-deployment-for-automatic-upgrades"></a>Nasazení vlnovkou pro automatické upgrady

V režimu automatického upgradu máte možnost Povolit cluster pro nasazení Wave. S nasazením vlnovkou můžete vytvořit kanál pro upgrade testovacích, fází a produkčních clusterů v pořadí, který je oddělený integrovaným zanesli časem, a ověřit nadcházející verze Service Fabric před tím, než se aktualizují produkční clustery.

### <a name="enable-wave-deployment"></a>Povolit nasazení Wave

> [!NOTE]
> Nasazení Wave vyžaduje `2020-12-01-preview` verzi rozhraní API (nebo novější) pro prostředek *Microsoft. ServiceFabric/clustery* .

Pokud chcete povolit nasazení Wave pro automatický upgrade, nejdřív určete, který Wave má cluster přiřadit:

* **Wave 0** ( `Wave0` ): clustery se aktualizují hned po vydání nového buildu Service Fabric. Určeno pro testovací a vývojové clustery.
* **Wave 1** ( `Wave1` ): clustery se aktualizují po jednom týdnu (sedm dní) po vydání nového sestavení. Určeno pro předem připravené a přípravné clustery.
* **Wave 2** ( `Wave2` ): clustery se aktualizují za dva týdny (14 dní) po uvolnění nového sestavení. Určeno pro produkční clustery.

Pak jednoduše přidejte `upgradeWave` vlastnost do šablony prostředků clusteru s jednou z výše uvedených hodnot Wave. Zajistěte, aby byla verze rozhraní API prostředků clusteru `2020-12-01-preview` nebo novější.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Po nasazení aktualizované šablony se cluster zaregistruje v zadaném formátu Wave pro další období upgradu a potom.

Můžete se [zaregistrovat k e-mailovým oznámením](#register-for-notifications) s odkazy na další pomoc v případě, že dojde k chybě upgradu clusteru.

### <a name="register-for-notifications"></a>Registrace oznámení

Můžete se zaregistrovat k oznámením v případě, že dojde k chybě upgradu clusteru. E-mail se pošle na vámi určené e-mailové adresy s dalšími podrobnostmi o selhání upgradu a odkazy na další pomoc.

> [!NOTE]
> Registrace v nasazení Wave není nutná k přijímání oznámení o selhání upgradu.

Chcete-li provést registraci v oznámeních, přidejte do `notifications` šablony prostředku clusteru oddíl a určete jednu nebo více e-mailových adres (*přijímačů*) pro příjem oznámení:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Po nasazení aktualizované šablony se zaregistrují oznámení o selhání upgradu.

## <a name="custom-policies-for-manual-upgrades"></a>Vlastní zásady pro ruční upgrady

Pro ruční upgrady clusteru můžete zadat vlastní zásady stavu. Tyto zásady se uplatní pokaždé, když vyberete novou verzi modulu runtime, která aktivuje systém za účelem zahájení upgradu clusteru. Pokud zásady nepřepisujete, použijí se výchozí hodnoty.

Můžete zadat vlastní zásady stavu nebo zkontrolovat aktuální nastavení v části **upgrade prostředků infrastruktury** v prostředku clusteru v Azure Portal tím, že pro **zásady upgradu** vyberete *vlastní* možnost.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="V části &quot;upgrady prostředků infrastruktury&quot; v prostředku clusteru v Azure Portal vyberte možnost vlastní zásady upgradu, aby se během upgradu nastavily vlastní zásady stavu.":::

## <a name="query-for-supported-cluster-versions"></a>Dotaz na podporované verze clusteru

Můžete použít [Azure REST API](/rest/api/azure/) k vypsání všech dostupných verzí modulu runtime pro Service Fabric ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) dostupných pro zadané umístění a vaše předplatné.

Další podrobnosti o podporovaných verzích a operačních systémech najdete také v referenčních [verzích Service Fabric](service-fabric-versions.md) .

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
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

`supportExpiryUtc`Ve výstupu sestav, pokud vyprší platnost dané vydané verze nebo vypršela její platnost. Nejnovější verze nebudou mít platné datum, ale místo hodnoty *9999-12-31T23:59:59.9999999*, což znamená, že datum vypršení platnosti ještě není nastavené.


## <a name="next-steps"></a>Další kroky

* [Správa upgradů Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Přizpůsobení [nastavení Service Fabric clusteru](service-fabric-cluster-fabric-settings.md)
* [Horizontální navýšení nebo navýšení kapacity clusteru](service-fabric-cluster-scale-in-out.md)
* Další informace o [upgradech aplikací](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
