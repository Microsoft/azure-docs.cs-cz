---
title: Příprava na změnu odchozí adresy IP
description: Pokud se vaše odchozí IP adresa změní, přečtěte si, co dělat, aby vaše aplikace po změně fungovala dál.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671666"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Jak se připravit na změnu odchozí IP adresy

Pokud jste obdrželi oznámení, že se mění odchozí IP adresy vaší aplikace Azure App Service, postupujte podle pokynů v tomto článku.

## <a name="determine-if-you-have-to-do-anything"></a>Zjistěte, zda musíte něco udělat

* Možnost 1: Pokud vaše aplikace App Service nepoužívá filtrování IP adres, explicitní seznam zahrnutí nebo speciální zpracování odchozích přenosů, jako je směrování nebo brána firewall, není nutná žádná akce.

* Možnost 2: Pokud má vaše aplikace zvláštní zpracování odchozích IP adres (viz příklady níže), přidejte nové odchozí IP adresy všude tam, kde se zobrazí stávající. Nenahrazujte existující IP adresy. Nové odchozí IP adresy najdete podle pokynů v další části.

  Například odchozí IP adresa může být explicitně zahrnuta do brány firewall mimo vaši aplikaci nebo externí platební služba může mít povolený seznam, který obsahuje odchozí IP adresu pro vaši aplikaci. Pokud je vaše odchozí adresa nakonfigurovaná v seznamu kdekoli mimo vaši aplikaci, je třeba to změnit.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Vyhledání odchozích IP adres na webu Azure Portal

Nové odchozí IP adresy jsou zobrazeny na portálu dříve, než se projeví. Když Azure začne používat nové, staré se už nebudou používat. Používá se pouze jedna sada najednou, takže položky v seznamech zahrnutí musí mít staré i nové IP adresy, aby se zabránilo výpadku, když dojde k přechodu. 

1.  Otevřete [portál Azure](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **Možnost Služby aplikací**.

3.  Ze seznamu vyberte aplikaci App Service.

1.  Pokud se jedná o aplikaci s funkcí, přečtěte si informace o [odchozích IP adresách aplikace](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  V záhlaví **Nastavení** klikněte v levém navigačním panelu na **Vlastnosti** a vyhledejte oddíl označený **Odchozí IP adresy**.

5. Zkopírujte ip adresy a přidejte je do speciálního zpracování odchozích přenosů, jako je například filtr nebo povolený seznam. Neodstraňujte existující adresy IP v seznamu.

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak se připravit na změnu IP adresy, která byla iniciována Azure. Další informace o IP adresách ve službě Azure App Service najdete v tématu [Příchozí a odchozí IP adresy ve službě Azure App Service](overview-inbound-outbound-ips.md).
