---
title: Logika zpracování pravidel Azure Firewall
description: Azure Firewall má pravidla NAT, pravidla sítě a pravidla aplikací. Pravidla jsou zpracována podle typu pravidla.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264775"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování pravidel Azure Firewall
Pravidla překladu a řízení o azure firewall můžete nakonfigurovat pravidla překladu a aplikací. Pravidla jsou zpracována podle typu pravidla. 

> [!NOTE]
> Pokud povolíte filtrování založené na inteligenci hrozeb, mají tato pravidla nejvyšší prioritu a jsou vždy zpracována jako první. Filtrování threat-intelligence může odepřít provoz před zpracováním všech nakonfigurovaných pravidel. Další informace najdete v tématu [filtrování založené na analýzách hrozeb azure firewall](threat-intel.md).

## <a name="outbound"></a>Odchozí

### <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla aplikací

Pokud nakonfigurujete síťová pravidla a pravidla aplikací, budou pravidla sítě použita v pořadí priorit před pravidly aplikace. Pravidla se ukončují. Pokud je tedy shoda nalezena v síťovém pravidle, nebudou zpracována žádná další pravidla.  Pokud neexistuje žádná shoda pravidel sítě a pokud je protokol HTTP, HTTPS nebo MSSQL, je paket vyhodnocen pravidly aplikace v pořadí podle priority. Pokud stále není nalezena žádná shoda, je paket vyhodnocen proti [kolekci pravidel infrastruktury](infrastructure-fqdns.md). Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="inbound"></a>Příchozí

### <a name="nat-rules"></a>Pravidla NAT

Příchozí připojení k Internetu lze povolit konfigurací překladu cílové síťové adresy (DNAT), jak je popsáno v [kurzu: Filtrování příchozích přenosů pomocí DNAT brány Azure firewall pomocí portálu Azure](tutorial-firewall-dnat.md). Pravidla překladu nanesení paměti jsou před síťovými pravidly použita jako priorita. Pokud je nalezena shoda, je přidáno implicitní odpovídající síťové pravidlo umožňující přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu.

Pravidla aplikace nejsou použita pro příchozí připojení. Pokud tedy chcete filtrovat příchozí přenosy HTTP/S, měli byste použít bránu firewall pro webové aplikace (WAF). Další informace najdete v tématu [Co je brána firewall pro webové aplikace Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Příklady

Následující příklady ukazují výsledky některých z těchto kombinací pravidel.

### <a name="example-1"></a>Příklad 1

Připojení k google.com je povoleno z důvodu odpovídajícího síťového pravidla.

**Síťové pravidlo**

- Akce: Povolit


|jméno  |Protocol (Protokol)  |Typ zdroje  |Zdroj  |Typ cíle  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Povolit web     |TCP|IP adresa|*|IP adresa|*|80,443

**Pravidlo aplikace**

- Akce: Odepřít

|jméno  |Typ zdroje  |Zdroj  |Protokol:Port|Cílové skupinové název nqdn|
|---------|---------|---------|---------|----------|----------|
|Odepřít-google     |IP adresa|*|http:80,https:443|google.com

**Výsledek**

Připojení k google.com je povoleno, protože paket odpovídá pravidlu *povolit webovou* síť. Zpracování pravidel se v tomto okamžiku zastaví.

### <a name="example-2"></a>Příklad 2

Provoz SSH je odepřen, protože ho blokuje kolekce pravidel sítě S vyšší *prioritu.*

**Kolekce síťových pravidel 1**

- Název: Povolit sběr
- Priorita: 200
- Akce: Povolit

|jméno  |Protocol (Protokol)  |Typ zdroje  |Zdroj  |Typ cíle  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Povolit-SSH     |TCP|IP adresa|*|IP adresa|*|22

**Kolekce síťových pravidel 2**

- Název: Deny-collection
- Priorita: 100
- Akce: Odepřít

|jméno  |Protocol (Protokol)  |Typ zdroje  |Zdroj  |Typ cíle  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Odepřít-SSH     |TCP|IP adresa|*|IP adresa|*|22

**Výsledek**

Připojení SSH jsou odepřena, protože je blokuje kolekce pravidel sítě s vyšší prioritou. Zpracování pravidel se v tomto okamžiku zastaví.

## <a name="rule-changes"></a>Změny pravidel

Pokud změníte pravidlo pro odepření dříve povoleného provozu, budou vynechány všechny relevantní existující relace.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat bránu Azure Firewall](tutorial-firewall-deploy-portal.md).