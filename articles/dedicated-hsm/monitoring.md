---
title: Možnosti monitorování – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Přehled možností monitorování vyhrazené HSM Azure a zodpovědnost za monitorování
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c3d062d615208177fcb9c7df511f598613bf6a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092699"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Vyhrazené monitorování HSM Azure

Vyhrazená služba HSM v Azure poskytuje fyzické zařízení pro použití výhradně pro zákazníky s úplnou zodpovědností řízení a správou. K dispozici je zařízení [Thales Luna 7 model HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms).  Společnost Microsoft nebude mít po zřízení prostřednictvím fyzického připojení sériového portu jako role monitorování žádný přístup správce. Výsledkem je, že zákazníci zodpovídají za typické provozní aktivity, včetně komplexního monitorování a analýzy protokolů.
Zákazníci jsou plně odpovědní za aplikace, které používají HSM, a měly by spolupracovat s Thales pro podporu nebo pomoc s asistencí. Vzhledem k rozsahu vlastnictví provozu zákazníka není možné, že společnost Microsoft nabízí pro tuto službu žádný druh záruky vysoké dostupnosti. Je zodpovědností zákazníka, aby bylo zajištěno, že jejich aplikace jsou správně nakonfigurovány tak, aby dosáhly vysoké dostupnosti. Microsoft bude monitorovat a udržovat stav zařízení a připojení k síti.

## <a name="microsoft-monitoring"></a>Sledování Microsoft

Používané zařízení HSM Thales Luna 7 má standardně SNMP a sériový port jako možnosti monitorování zařízení. Microsoft použil připojení sériového portu jako fyzický způsob, jak se připojit k zařízení a načíst základní telemetrii na stav zařízení. To zahrnuje položky, jako je například teplota a stav komponenty, jako jsou napájení a ventilátory.
K tomu Microsoft používá pro zařízení Thales nastavenou roli monitorování bez možnosti správy. Tato role umožňuje načíst telemetrii, ale neposkytuje žádnému přístupu k zařízení z hlediska úlohy správy nebo jakýmkoli způsobem, jak se bude zobrazovat kryptografické informace. Naši zákazníci si můžou zajistit, aby jejich zařízení bylo skutečně vlastní pro správu, správu a používání citlivých úložišť kryptografických klíčů. V případě, že některý zákazník není spokojen s tímto minimálním přístupem pro základní monitorování stavu, má možnost zakázat účet monitorování. Zjevnou příčinou je to, že společnost Microsoft nebude mít žádné informace, a proto nebude moci poskytovat žádná proaktivní oznámení o problémech se stavem zařízení. V takové situaci je zákazník zodpovědný za stav zařízení.
Tato funkce monitorování je nastavená tak, aby se při získávání dat o stavu do zařízení dotazoval každých 10 minut. Vzhledem k chybě, která je náchylná k chybám, se může vygenerovat výstraha až po několika negativních indikátorech stavu v průběhu jedné hodiny. Tato výstraha by nakonec vedla k tomu, že se problém upozorní na proaktivní zákaznickou komunikaci.
V závislosti na povaze problému by se měla provést příslušná akce, aby se snížil dopad a zajistila se nízká náprava rizik. Například selhání zdroje napájení je postup horkého swapu, který nemá žádnou výslednou událost bez zásahu, takže může být proveden s nízkým dopadem a minimálním rizikem na operaci. Další postupy mohou vyžadovat, aby zařízení bylo nulové a zrušit, aby bylo možné minimalizovat jakékoli bezpečnostní riziko pro zákazníka. V takové situaci by zákazník zřídil alternativní zařízení, aby se znovu připojil k párování vysoké dostupnosti, takže se aktivuje synchronizace zařízení. Normální operace by obnovila minimální čas s minimálním přerušením a nejnižším bezpečnostním rizikem.  

## <a name="customer-monitoring"></a>Monitorování zákazníků

Hodnota umístění vyhrazené služby HSM je ovládací prvek, který zákazník získá, zejména s ohledem na to, že se jedná o zařízení, které je v cloudu dodáváno. V důsledku tohoto řízení je možné sledovat a spravovat stav zařízení. Zařízení HSM Thales Luna 7 obsahuje pokyny pro implementaci SNMP a syslog. Zákazníkům vyhrazené služby HSM se doporučuje použít tuto službu i v případě, že účet Microsoft monitor zůstane aktivní a měl by ho považovat za povinný, pokud účet Microsoft monitoru zakáže.
Kterákoli z dostupných technik by umožnila zákazníkovi identifikovat problémy a volat podporu Microsoftu, aby zahájili odpovídající práci na nápravě.

## <a name="next-steps"></a>Další kroky

Doporučuje se, aby všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení, byly dobře pochopitelné před jakýmkoli zřizováním a návrhem aplikace a nasazením aplikací. Další témata o úrovni konceptu:

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
