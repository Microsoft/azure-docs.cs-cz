---
title: Filtr IP adres Azure IoT Hub Classic (zastaralé) | Microsoft Docs
description: Postup upgradu z klasického filtru IP adres a jaké jsou výhody
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661150"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT Hub klasický filtr IP adres a postup upgradu 

Upgradovaný filtr IP pro IoT Hub chrání integrovaný koncový bod a je ve výchozím nastavení zabezpečený. I když se snažíme nikdy udělat zásadní změny, rozšířený model zabezpečení filtru IP adres je nekompatibilní s filtrem klasických IP adres, takže oznamujeme jeho vyřazení. Další informace o novém inovovaném filtru IP adres najdete v tématu [co je nového](#whats-new) a [filtry IP adres centra IoT](iot-hub-ip-filtering.md).

Abyste se vyhnuli přerušení služby, musíte provést upgrade s asistencí před konečným termínem migrace. v takovém případě se upgrade provede automaticky. Další informace o časové ose migrace najdete v tématu [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>Postup upgradu

1.  Navštívit Azure Portal
2.  Přejděte do svého centra IoT.
3.  V nabídce na levé straně vyberte **síť** .
4.  Měla by se zobrazit zpráva s výzvou k upgradu filtru IP na nový model. Vyberte **Ano**, abyste mohli pokračovat.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Obrázek znázorňující úvodní výzvu k upgradu z klasického filtru IP":::
5.  Vzhledem k tomu, že nový filtr IP blokuje veškerou IP adresu ve výchozím nastavení, upgrade odebere vaše jednotlivá pravidla zamítnutí, ale nabídne možnost je před uložením zkontrolovat. Pečlivě zkontrolujte pravidla, abyste se ujistili, že budou fungovat za vás.
6.  Podle pokynů dokončete upgrade.

## <a name="whats-new"></a>Novinky

### <a name="secure-by-default"></a>Zabezpečení ve výchozím nastavení

Klasický filtr IP adres implicitně umožňuje všem IP adresám připojení k IoT Hub ve výchozím nastavení, což není správně zarovnané na nejběžnější scénáře zabezpečení sítě. Obvykle byste chtěli, aby se ke službě IoT Hub připojovaly jenom důvěryhodné IP adresy a odmítly všechno ostatní. Aby bylo možné tento cíl dosáhnout pomocí klasického filtru IP, je to proces s více kroky. Například pokud chcete přijímat jenom provoz z `192.168.100.0/22` , musíte

1. Nakonfigurujte jedno pravidlo *Povolení* pro `192.168.100.0/22` .
1. Konfigurace jiného pravidla *blokování* pro `0.0.0.0/0` (pravidlo Blokovat vše)
1. Ujistěte se, že jsou pravidla seřazená správně, přičemž pravidlo povolení je seřazené nad pravidlem blokování.

V praxi tento proces více kroků způsobuje nejasnost. Uživatelé nenakonfigurovali pravidlo Blokovat vše nebo správně nezměnila pravidla. Výsledkem je nezamýšlené vystavení. 

Nové filtry IP zablokují všechny IP adresy ve výchozím nastavení. K IoT Hub se smí připojit pouze rozsahy IP adres, které výslovně přidáte. V předchozím příkladu už nejsou potřeba kroky 2 a 3. Toto nové chování zjednodušuje konfiguraci a společnost [zabezpečení ve výchozím principu](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Ochrana integrovaného koncového bodu kompatibilního centra událostí

Pro integrovaný koncový bod nelze použít klasický filtr IP adres. Toto omezení znamená, že událost s nakonfigurovaným pravidlem blokování všech pravidel (Block `0.0.0.0/0` ) je předdefinovaný koncový bod stále přístupný z libovolné IP adresy.

Nový filtr IP adres poskytuje možnost použít pravidla na integrovaný koncový bod, což snižuje riziko ohrožení zabezpečení sítě.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Obrázek znázorňující přepínač, který se má použít u předdefinovaného koncového bodu.":::

> [!NOTE]
> Tato možnost není k dispozici zdarma (F1) centra IoT. Pokud chcete použít pravidla filtru IP na předdefinovaný koncový bod, použijte placené centrum IoT.

### <a name="api-impact"></a>Dopad rozhraní API

Upgradovaný filtr IP adres je k dispozici v IoT Hub rozhraní API prostředků od `2020-08-31` (stejně jako `2020-08-31-preview` ) a dále. Klasický filtr IP adres je stále k dispozici ve všech verzích rozhraní API, ale v budoucí verzi rozhraní API se v blízkosti termínu migrace odebere. Další informace o časové ose migrace najdete v tématu [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Tip: zkuste změny před tím, než se použijí.

Vzhledem k tomu, že nový filtr IP blokuje všechny IP adresy ve výchozím nastavení, nebudou jednotlivá pravidla blokování kompatibilní. Proces upgradu s asistencí proto tato pravidla jednotlivých blokování odstraní. 

Postup při pokusu o změnu v rámci klasického filtru IP:

1. Navštivte kartu **sítě** ve službě IoT Hub.
1. Poznamenejte si existující konfiguraci filtru IP (Classic) pro případ, že chcete vrátit zpět
1. U pole pravidla s **blokem** vyberte ikonu koše a odstraňte je.
1. Níže přidejte další pravidlo s `0.0.0.0/0` a vyberte možnost **blok**
1. Vyberte **Uložit**.

Tato konfigurace napodobá způsobu, jakým se nový filtr IP chová po upgradu z klasického rozhraní. Jedinou výjimkou je integrovaná ochrana koncových bodů, kterou není možné zkusit použít pro klasický filtr IP adres. Tato funkce je však volitelná, takže ji nemusíte používat, pokud se domníváte, že by mohla něco poškodit.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Tip: Ověřte diagnostické protokoly pro všechna připojení IP k vašemu centru IoT.

Chcete-li zajistit hladký přechod, zkontrolujte protokoly diagnostiky v kategorii připojení. Vyhledejte `maskedIpAddress` vlastnost a podívejte se, jestli rozsahy neodpovídají očekávání. Nezapomeňte: nový filtr IP adres zablokuje všechny IP adresy, které se nepřidaly explicitně.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>Dokumentace k IoT Hub klasického filtru IP (vyřazeno)

> [!IMPORTANT]
> Níže je uvedená původní dokumentace pro klasický filtr IP adres, která je vyřazená.

Zabezpečení je důležitým aspektem řešení IoT založeného na Azure IoT Hub. Někdy je potřeba v rámci konfigurace zabezpečení explicitně zadat IP adresy, ze kterých se smějí připojovat zařízení. Funkce *filtrování IP adres* umožňuje konfigurovat pravidla pro zamítnutí nebo přijetí provozu z konkrétních adres IPv4.

### <a name="when-to-use"></a>Kdy je použít

Existují dva konkrétní případy použití, když je užitečné zablokovat IoT Hub koncových bodů pro určité IP adresy:

* Vaše centrum IoT by mělo přijímat přenosy jenom z určeného rozsahu IP adres a zamítnout všechno ostatní. Například používáte službu IoT Hub se službou [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) k vytváření privátních připojení mezi službou IoT Hub a místní infrastrukturou.

* Musíte odmítnout provoz z IP adres, které správce služby IoT Hub identifikoval jako podezřelé.

### <a name="how-filter-rules-are-applied"></a>Způsob uplatňování pravidel filtru

Pravidla filtru IP jsou použita na úrovni služby IoT Hub. Proto se pravidla filtru IP použijí na všechna připojení ze zařízení a back-endové aplikace s využitím jakéhokoli podporovaného protokolu. Nicméně klienti načtou přímo z [integrovaného koncového bodu kompatibilního](iot-hub-devguide-messages-read-builtin.md) s centrem událostí (ne prostřednictvím připojovacího řetězce IoT Hub) nejsou vázány na pravidla filtru IP. 

Jakýkoli pokus o připojení z IP adresy, která odpovídá pravidlu odmítnutí protokolu IP ve službě IoT Hub, obdrží ne401 autorizovaný kód stavu a popis. Zpráva odpovědi neinformuje o pravidle IP adres. Odmítání IP adres může zabránit jiným službám Azure, jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer v Azure Portal z interakce se službou IoT Hub.

> [!NOTE]
> Pokud musíte použít Azure Stream Analytics (ASA) ke čtení zpráv ze služby IoT Hub s povoleným filtrem IP adres, použijte název a koncový bod služby IoT Hub, který je kompatibilní s centrem událostí, a ručně přidejte [vstup datového proudu Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) do ASA.

### <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je mřížka **filtru IP** na portálu pro Centrum IoT prázdná. Toto výchozí nastavení znamená, že vaše centrum akceptuje připojení z libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0.

Pokud se chcete dostat na stránku nastavení filtru IP adres, vyberte **sítě**, **veřejný přístup** a pak zvolte **vybrané rozsahy IP adres**:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Přidání nebo úprava pravidla filtru IP

Pokud chcete přidat pravidlo filtru IP, vyberte **+ Přidat pravidlo filtru IP adres**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Přidání pravidla filtru IP do služby IoT Hub":::

Po výběru možnosti **Přidat pravidlo filtru IP adres** vyplňte zobrazená pole.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Po výběru možnosti Přidat pravidlo filtru IP adres":::

* Zadejte **název** pravidla filtru IP adres. Musí se jednat o jedinečný alfanumerický řetězec s délkou do 128 znaků, ve kterém se nerozlišují malá a velká písmena. Přípustné jsou jenom 7bitové alfanumerické znaky ASCII a znaky `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Zadejte jednu IPv4 adresu nebo blok IP adres v zápisu CIDR. Například adresa 192.168.100.0/22 v zápisu CIDR odpovídá 1024 IPv4 adresám od 192.168.100.0 do 192.168.103.255.

* Jako **akci** pravidla filtru IP adres vyberte **Povolit** nebo **Blokovat**.

Po vyplnění polí pravidlo uložte výběrem položky **Uložit**. Zobrazí se upozornění s informací, že probíhá aktualizace.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Oznámení o uložení pravidla filtru IP":::

Když dosáhnete maximálního počtu 10 pravidel filtru IP adres, možnost **Přidat** už nebude aktivní.

Pokud chcete upravit stávající pravidlo, vyberte údaje, které chcete změnit, proveďte změnu a pak úpravy uložte výběrem položky **Uložit**.

### <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP adres

Pokud chcete některé pravidlo filtru IP adres odstranit, vyberte na příslušném řádku ikonu odpadkového koše a pak vyberte možnost **Uložit**. Dané pravidlo se odebere a změna se uloží.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Odstraní pravidlo filtru IP IoT Hub.":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Načtení a aktualizace filtrů IP adres pomocí Azure CLI

Filtry IP IoT Hub můžete načíst a aktualizovat prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/).

Chcete-li načíst aktuální filtry IP IoT Hub, spusťte příkaz:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Tato akce vrátí objekt JSON, ve kterém jsou uvedené existující filtry IP adres pod `properties.ipFilterRules` klíčem:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Chcete-li přidat nový filtr IP adres pro IoT Hub, spusťte příkaz:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Pokud chcete ve svém IoT Hub odebrat existující filtr IP adres, spusťte:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Všimněte si, že `<ipFilterIndexToRemove>` musí odpovídat řazení filtrů IP adres v IoT Hub `properties.ipFilterRules` .

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Načtení a aktualizace filtrů IP adres pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Filtry IP IoT Hub můžete načíst a nastavit prostřednictvím [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>Aktualizace pravidel filtru IP pomocí REST

Můžete také načíst a upravit filtr IP IoT Hub pomocí koncového bodu REST poskytovatele prostředků Azure. Projděte si část `properties.ipFilterRules` v tématu o [metodě createorupdate](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidel filtru IP adres

Pravidla filtru IP adres se uplatňují jedno pod druhém a akci přijetí nebo odmítnutí určí první pravidlo odpovídající příslušné IP adrese.

Pokud například chcete přijmout adresy v rozsahu 192.168.100.0/22 a všechny ostatní odmítnout, první pravidlo v mřížce by mělo přijmout rozsah adres 192.168.100.0/22. Další pravidlo by mělo pomocí rozsahu 0.0.0.0/0 odmítnout všechny adresy.

Pořadí pravidel filtru IP adres v mřížce můžete změnit kliknutím na tři svislé tečky na začátku řádku a přetažením.

Pokud chcete uložit nové pořadí pravidel filtru IP adres, klikněte na **Uložit**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="Změna pořadí pravidel filtru IP IoT Hub":::

## <a name="next-steps"></a>Další kroky

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Použití filtrů IP adres](iot-hub-ip-filtering.md)