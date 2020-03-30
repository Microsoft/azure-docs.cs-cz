---
title: Možnosti monitorování – Azure Dedicated HSM | Dokumenty společnosti Microsoft
description: Přehled možností monitorování azure dedicated modulu hardwarového zabezpečení a odpovědnosti monitorování
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881416"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure Vyhrazené monitorování modulu hardwarového zabezpečení

Služba Azure Dedicated HSM Service poskytuje fyzické zařízení pro použití jediným zákazníkem s úplnou odpovědností za správu a správu. K dispozici je dostupný přístroj [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Společnost Microsoft nebude mít po zřízení zákazníkem žádný přístup pro správu, kromě fyzické přílohy sériového portu jako role monitorování. V důsledku toho jsou zákazníci zodpovědní za typické provozní činnosti včetně komplexního monitorování a analýzy protokolů.
Zákazníci jsou plně zodpovědní za aplikace, které používají skryté funkce dohledu, a měli by spolupracovat se společností Gemalto na podpoře nebo konzultační pomoci. Vzhledem k rozsahu vlastnictví provozní hygieny zákazníkem není možné, aby společnost Microsoft nabídla pro tuto službu jakoukoli záruku vysoké dostupnosti. Je odpovědností zákazníka zajistit, aby jejich aplikace byly správně nakonfigurovány tak, aby dosahovaly vysoké dostupnosti. Společnost Microsoft bude monitorovat a udržovat stav zařízení a připojení k síti.

## <a name="microsoft-monitoring"></a>Monitorování společnosti Microsoft

Používá se zařízení Gemalto SafeNet má ve výchozím nastavení SNMP a sériový port jako možnosti pro sledování zařízení. Společnost Microsoft použila připojení sériového portu jako fyzický prostředek pro připojení k zařízení k načtení základní telemetrie na stavu zařízení. To zahrnuje položky, jako je teplota a stav součásti, jako jsou napájecí zdroje a ventilátory.
K dosažení tohoto cíle používá společnost Microsoft roli "monitor" bez oprávnění správce nastavenou na zařízení Gemalto. Tato role poskytuje možnost načíst telemetrii, ale neposkytuje žádný přístup k zařízení z hlediska úlohy správy nebo v žádném případě prohlížení kryptografických informací. Naši zákazníci si mohou být jisti, že jejich zařízení je skutečně jejich vlastní pro správu, správu a použití pro citlivé úložiště kryptografických klíčů. V případě, že zákazník není spokojen s tímto minimálním přístupem pro základní monitorování stavu, mají možnost zakázat monitorovací účet. Zřejmým důsledkem toho je, že společnost Microsoft nebude mít žádné informace, a proto není schopna poskytovat žádné proaktivní oznámení o zdravotních problémech zařízení. V takovém případě je zákazník zodpovědný za stav zařízení.
Samotná funkce monitoru je nastavena tak, aby každých 10 minut vyzývala zařízení, aby získala data o stavu. Vzhledem k chybě náchylné povahy sériové komunikace, pouze po více negativních ukazatelů zdravotnístav by za dobu jedné hodiny výstraha být zvýšena. Toto upozornění by nakonec vedlo k proaktivní komunikaci se zákazníky, která by problém oznamovala.
V závislosti na povaze problému by byl přijat vhodný postup ke snížení dopadu a zajištění sanace nízkého rizika. Například selhání napájení je postup výměny za provozu bez výsledné události manipulace, takže lze provést s nízkým dopadem a minimálním rizikem provozu. Jiné postupy mohou vyžadovat, aby zařízení bylo vynulováno a zrušeno, aby se minimalizovalo bezpečnostní riziko pro zákazníka. V takovém případě by zákazník zřídit alternativní zařízení, znovu připojit spárování s vysokou dostupností a tím spouštění synchronizace zařízení. Normální provoz by se obnovil v minimálním čase, s minimálním narušením a nejnižším bezpečnostním rizikem.  

## <a name="customer-monitoring"></a>Monitorování zákazníků

Hodnota návrh vyhrazené služby hardwarového zabezpečení je ovládací prvek zákazník získá zařízení, zejména s ohledem na to, že se jedná o cloud dodané zařízení. Důsledkem tohoto ovládacího prvku je odpovědnost za sledování a správu stavu zařízení. Zařízení Gemalto SafeNet je dodáváno s pokyny pro implementaci SNMP a Syslog. Zákazníkům vyhrazené služby hardwarového zabezpečení se doporučuje používat i v případě, že účet sledování Microsoft udála aktivní a měl by to považovat za povinný, pokud účet sledování Microsoftu zakáže.
Buď technika k dispozici by umožnila zákazníkovi identifikovat problémy a volání podpory společnosti Microsoft zahájit příslušné nápravné práce.

## <a name="next-steps"></a>Další kroky

Doporučuje se, aby všechny klíčové koncepty služby, jako je například vysoká dostupnost a zabezpečení, byly dobře pochopeny před jakýmkoli zřizováním zařízení a návrhem nebo nasazením aplikací. Další témata na úrovni konceptu:

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Síťové služby](networking.md)
* [Možnosti podpory](supportability.md)
