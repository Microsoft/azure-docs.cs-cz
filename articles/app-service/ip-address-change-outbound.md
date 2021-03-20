---
title: Příprava na změnu odchozí IP adresy
description: Pokud se vaše odchozí IP adresa bude měnit, zjistěte, co dělat, aby vaše aplikace po provedení změny i nadále fungovala.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74671666"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Příprava na změnu odchozí IP adresy

Pokud jste obdrželi oznámení o změně odchozích IP adres vaší aplikace Azure App Service, postupujte podle pokynů v tomto článku.

## <a name="determine-if-you-have-to-do-anything"></a>Určení, jestli je nutné provést cokoli

* Možnost 1: Pokud vaše aplikace App Service nepoužívá filtrování IP adres, explicitní seznam zahrnutí nebo speciální zpracování odchozích přenosů, jako je směrování nebo brána firewall, nevyžaduje se žádná akce.

* Možnost 2: Pokud vaše aplikace má speciální zacházení s odchozími IP adresami (viz příklady níže), přidejte nové odchozí IP adresy, pokud se zobrazí stávající. Neměňte stávající IP adresy. Nové odchozí IP adresy můžete najít podle pokynů v následující části.

  Odchozí IP adresa může být například explicitně obsažena v bráně firewall mimo vaši aplikaci, nebo externí platební služba může mít povolený seznam, který obsahuje odchozí IP adresu vaší aplikace. Pokud je vaše odchozí adresa nakonfigurovaná v seznamu kdekoli mimo vaši aplikaci, musí se změnit.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Najít odchozí IP adresy v Azure Portal

Nové odchozí IP adresy se zobrazí na portálu, než začnou platit. Když Azure začne používat nové, staré se už nebudou používat. V jednom okamžiku se používá jenom jedna sada, takže položky v seznamech zahrnutí musí mít staré i nové IP adresy, aby se zabránilo výpadku, když dojde k přepnutí. 

1.  Otevřete [Azure Portal](https://portal.azure.com).

2.  V navigační nabídce na levé straně vyberte **App Services**.

3.  Ze seznamu vyberte svou aplikaci App Service.

1.  Pokud je aplikace Function App, přečtěte si téma aplikace [Function Apps odchozí IP adresy](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  V záhlaví **Nastavení** klikněte na tlačítko **vlastnosti** v levém navigačním panelu a vyhledejte část s názvem **odchozí IP adresy**.

5. Zkopírujte IP adresy a přidejte je do speciálního zpracování odchozích přenosů, jako je filtr nebo seznam povolených. V seznamu neodstraňujte existující IP adresy.

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak připravit na změnu IP adresy, kterou iniciovala Azure. Další informace o IP adresách v Azure App Service najdete [v tématu příchozí a odchozí IP adresy v Azure App Service](overview-inbound-outbound-ips.md).
