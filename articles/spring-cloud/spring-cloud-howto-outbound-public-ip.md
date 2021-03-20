---
title: Jak identifikovat odchozí veřejné IP adresy v Azure jaře cloudu
description: Postup zobrazení statických odchozích veřejných IP adres ke komunikaci s externími prostředky, jako jsou databáze, úložiště, Key Vault atd.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94663584"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Jak identifikovat odchozí veřejné IP adresy v Azure jaře cloudu

Na této stránce najdete popis postupu zobrazení statických odchozích veřejných IP adres aplikací pro jarní Cloud v Azure.  Veřejné IP adresy se používají ke komunikaci s externími prostředky, jako jsou databáze, úložiště a trezory klíčů.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Jak IP adresy fungují v Azure jaře cloudu

Jarní cloudová služba Azure má jednu nebo víc odchozích veřejných IP adres. Počet odchozích veřejných IP adres se může lišit v závislosti na úrovních a dalších faktorech. 

Odchozí veřejné IP adresy jsou obvykle konstantované a zůstávají stejné, ale existují výjimky.

## <a name="when-outbound-ips-change"></a>Změna odchozích IP adres

Každá instance služby jarního cloudu Azure má v daném okamžiku nastavený počet odchozích veřejných IP adres. Jakékoli odchozí připojení z aplikací, jako je například databáze back-end, používá jako zdrojovou IP adresu jednu z odchozích veřejných IP adres. IP adresa se vybere náhodně za běhu, takže vaše back-end služba musí bránu firewall otevřít na všechny odchozí IP adresy.

Počet odchozích veřejných IP adres se změní, když provedete jednu z následujících akcí:

- Upgradujte si instanci cloudu jarní Azure mezi úrovněmi.
- Vyvolejte lístek podpory pro více odchozích veřejných IP adres pro obchodní potřeby.

## <a name="find-outbound-ips"></a>Najít odchozí IP adresy

Pokud chcete najít odchozí veřejné IP adresy, které aktuálně používá vaše instance služby v Azure Portal, klikněte na **sítě** v levém navigačním podokně vaší instance. Jsou uvedeny v poli **odchozí IP adresy** .

Stejné informace můžete najít spuštěním následujícího příkazu v Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
* [Další informace o spravovaných identitách pro prostředky Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Další informace o trezoru klíčů v Azure jaře cloudu](spring-cloud-tutorial-managed-identities-key-vault.md)
