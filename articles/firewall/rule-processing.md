---
title: Logika zpracování pravidel Azure Firewall
description: Azure Firewall obsahuje pravidla překladu adres (NAT), pravidla sítě a aplikace. Pravidla se zpracovávají podle typu pravidla.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 84110e749dac9267e994385aa5f6d05e3ba224a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087539"
---
# <a name="configure-azure-firewall-rules"></a>Konfigurace pravidel Azure Firewall
Pravidla překladu adres (NAT), pravidla sítě a aplikace můžete nakonfigurovat na Azure Firewall. Kolekce pravidel se zpracovávají podle typu pravidla v pořadí podle priority, nižších čísel od 100 do 65 000. Název kolekce pravidel může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. Musí začínat písmenem nebo číslicí a končit písmenem, číslicí nebo podtržítkem. Maximální délka názvu je 80 znaků.

Doporučujeme, abyste v případě potřeby nasadili čísla priority kolekce pravidel v 100 přírůstcích (100, 200, 300 a tak dále).

> [!NOTE]
> Pokud povolíte filtrování na základě logiky hrozeb, tato pravidla mají nejvyšší prioritu a jsou vždy zpracována jako první. Filtrování logiky hrozeb může před zpracováním všech nakonfigurovaných pravidel odepřít provoz. Další informace najdete v tématu [Azure firewall filtrování na základě logiky hrozeb](threat-intel.md).

## <a name="outbound-connectivity"></a>Odchozí připojení

### <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla pro aplikace

Pokud konfigurujete Síťová pravidla a pravidla pro aplikace, budou se Síťová pravidla aplikována v pořadí podle priority před pravidly aplikací. Pravidla se ukončí. Takže pokud se shoda najde v síťovém pravidle, nezpracovávají se žádná další pravidla.  Pokud se neshoduje žádné pravidlo sítě, a pokud je protokol HTTP, HTTPS nebo MSSQL, pak se paket vyhodnotí podle pravidel aplikace v pořadí podle priority. Pokud se pořád nenajde žádná shoda, vyhodnotí se paket na základě [kolekce pravidel infrastruktury](infrastructure-fqdns.md). Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="inbound-connectivity"></a>Příchozí připojení

### <a name="nat-rules"></a>Pravidla překladu adres (NAT)

Příchozí připojení k Internetu můžete povolit konfigurací překladu cílové sítě (DNAT), jak je popsáno v [kurzu: filtrování příchozího provozu pomocí Azure firewall DNAT pomocí Azure Portal](tutorial-firewall-dnat.md). Pravidla překladu adres (NAT) se aplikují přednostně před pravidly sítě. Pokud se najde shoda, přidá se implicitní odpovídající síťové pravidlo, které povolí přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu.

Pro příchozí připojení se nepoužijí pravidla aplikací. Takže pokud chcete filtrovat příchozí přenosy HTTP/S, měli byste použít Firewall webových aplikací (WAF). Další informace najdete v tématu [co je firewall webových aplikací Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Příklady

Následující příklady znázorňují výsledky některých z těchto kombinací pravidel.

### <a name="example-1"></a>Příklad 1

Připojení k google.com je povolené z důvodu odpovídajícího síťového pravidla.

**Síťové pravidlo**

- Akce: Povolit


|name  |Protokol  |Typ zdroje  |Zdroj  |Cílový typ  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Povolení – Web     |TCP|IP adresa|*|IP adresa|*|80,443

**Pravidlo aplikace**

- Akce: Deny

|name  |Typ zdroje  |Zdroj  |Protokol: port|Cílové plně kvalifikované názvy domén|
|---------|---------|---------|---------|----------|----------|
|Odepřít – Google     |IP adresa|*|http: 80, https: 443|google.com

**Výsledek**

Připojení k google.com je povoleno, protože paket se shoduje s pravidlem " *Povolení-webové* sítě". Zpracování pravidla se v tomto okamžiku zastaví.

### <a name="example-2"></a>Příklad 2

Přenosy SSH se zamítly, protože kolekce *síťových pravidel* s vyšší prioritou je blokuje.

**Kolekce pravidel sítě 1**

- Název: Allow-Collection
- Priorita: 200
- Akce: Povolit

|name  |Protokol  |Typ zdroje  |Zdroj  |Cílový typ  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Povolení – SSH     |TCP|IP adresa|*|IP adresa|*|22

**Kolekce pravidel sítě 2**

- Název: Deny-Collection
- Priorita: 100
- Akce: Deny

|name  |Protokol  |Typ zdroje  |Zdroj  |Cílový typ  |Cílová adresa  |Cílové porty|
|---------|---------|---------|---------|----------|----------|--------|
|Odepřít – SSH     |TCP|IP adresa|*|IP adresa|*|22

**Výsledek**

Připojení SSH jsou odepřena, protože je zablokovala kolekce pravidel sítě s vyšší prioritou. Zpracování pravidla se v tomto okamžiku zastaví.

## <a name="rule-changes"></a>Změny pravidla

Pokud změníte pravidlo na odepřít dříve povolený provoz, všechny relevantní existující relace budou zrušeny.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).