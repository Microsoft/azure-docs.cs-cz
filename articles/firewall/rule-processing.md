---
title: Logika zpracování pravidel Azure Firewall
description: Azure Firewall má pravidla NAT, pravidla sítě a pravidla aplikací. Pravidla jsou zpracována podle typu pravidla.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113437"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování pravidel Azure Firewall
Pravidla překladu a řízení o azure firewall můžete nakonfigurovat pravidla překladu a aplikací. Kolekce pravidel jsou zpracovány podle typu pravidla v pořadí podle priority, nižší čísla na vyšší čísla od 100 do 65 000. Název kolekce pravidel může mít pouze písmena, čísla, podtržítka, tečky nebo pomlčky. Musí začínat písmenem nebo číslem a končit písmenem, číslem nebo podtržítkem. Maximální délka názvu je 80 znaků.

Zpočátku je vhodné rozmístit čísla priorit kolekce pravidel ve 100 krocích (100, 200, 300 a tak dále), abyste měli v případě potřeby prostor pro přidání dalších kolekcí pravidel.

> [!NOTE]
> Pokud povolíte filtrování založené na inteligenci hrozeb, mají tato pravidla nejvyšší prioritu a jsou vždy zpracována jako první. Filtrování threat-intelligence může odepřít provoz před zpracováním všech nakonfigurovaných pravidel. Další informace najdete v tématu [filtrování založené na analýzách hrozeb azure firewall](threat-intel.md).

## <a name="outbound-connectivity"></a>Odchozí připojení

### <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla aplikací

Pokud nakonfigurujete síťová pravidla a pravidla aplikací, budou pravidla sítě použita v pořadí priorit před pravidly aplikace. Pravidla se ukončují. Pokud je tedy shoda nalezena v síťovém pravidle, nebudou zpracována žádná další pravidla.  Pokud neexistuje žádná shoda pravidel sítě a pokud je protokol HTTP, HTTPS nebo MSSQL, je paket vyhodnocen pravidly aplikace v pořadí podle priority. Pokud stále není nalezena žádná shoda, je paket vyhodnocen proti [kolekci pravidel infrastruktury](infrastructure-fqdns.md). Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="inbound-connectivity"></a>Příchozí připojení

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