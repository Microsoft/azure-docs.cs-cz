---
title: Doporučení zabezpečení
description: Implementujte doporučení zabezpečení, která vám pomůžou splnit vaše povinnosti zabezpečení, jak uvádí náš sdílený model zodpovědnosti. Zvyšte zabezpečení své aplikace.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 9f8fba617eb0274a8db7adc5d36c6ff50be10924
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761375"
---
# <a name="security-recommendations-for-app-service"></a>Doporučení zabezpečení pro App Service

Tento článek obsahuje doporučení zabezpečení pro Azure App Service. Implementace těchto doporučení vám pomůže splnit vaše povinnosti zabezpečení, jak je popsáno v našem sdíleném modelu zodpovědnosti, a zvýší celkové zabezpečení vašich řešení webových aplikací. Další informace o tom, co Microsoft potřebuje k plnění zodpovědností od poskytovatele služeb, najdete v článku [zabezpečení infrastruktury Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Obecné

| Doporučení | Komentáře |
|-|-|----|
| Stále aktuální | Používejte nejnovější verze podporovaných platforem, programovacích jazyků, protokolů a platforem. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře |
|-|----|
| Zakázat anonymní přístup | Pokud nepotřebujete podporovat anonymní žádosti, zakažte anonymní přístup. Další informace o možnostech ověřování Azure App Service najdete v tématu [ověřování a autorizace v Azure App Service](overview-authentication-authorization.md).|
| Vyžadovat ověření | Kdykoli je to možné, použijte modul App Service Authentication namísto psaní kódu pro zpracování ověřování a autorizaci. Viz [ověřování a autorizace v Azure App Service](overview-authentication-authorization.md). |
| Ochrana prostředků back-endu pomocí ověřeného přístupu | Můžete buď použít identitu uživatele, nebo použít identitu aplikace k ověření u prostředku back-endu. Pokud se rozhodnete použít identitu aplikace, použijte [spravovanou identitu](overview-managed-identity.md).
| Vyžadovat ověření certifikátu klienta | Ověřování klientským certifikátem vylepšuje zabezpečení tím, že povoluje jenom připojení z klientů, která se dají ověřit pomocí certifikátů, které poskytnete. |

## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře |
|-|-|
| Přesměrovat HTTP na HTTPs | Ve výchozím nastavení se klienti mohou připojit k webovým aplikacím pomocí protokolu HTTP nebo HTTPS. Doporučujeme přesměrovat HTTP na HTTPs, protože HTTPS používá protokol SSL/TLS k zajištění zabezpečeného připojení, které je šifrované i ověřené. |
| Šifrování komunikace s prostředky Azure | Když se vaše aplikace připojí k prostředkům Azure, jako je [SQL Database](https://azure.microsoft.com/services/sql-database/) nebo [Azure Storage](../storage/index.yml), připojení zůstane v Azure. Vzhledem k tomu, že připojení prochází přes sdílené sítě v Azure, měli byste vždycky zašifrovat veškerou komunikaci. |
| Vyžadovat nejnovější možnou verzi TLS | Vzhledem k tomu, že 2018 nové aplikace Azure App Service používají TLS 1,2. Novější verze TLS zahrnují vylepšení zabezpečení starších verzí protokolů. |
| Použití FTPS | App Service podporuje FTP i FTPS pro nasazování souborů. Pokud je to možné, používejte FTPS místo FTP. Pokud se jeden nebo oba tyto protokoly nepoužívají, měli byste [je zakázat](deploy-ftp.md#enforce-ftps). |
| Zabezpečení dat aplikací | Neukládejte tajné klíče aplikace, jako jsou přihlašovací údaje databáze, tokeny API nebo privátní klíče, do kódu nebo konfiguračních souborů. Běžně přijímaným přístupem je přístup k těmto [proměnným prostředí](https://wikipedia.org/wiki/Environment_variable) pomocí standardního vzoru ve zvoleném jazyce. V Azure App Service můžete definovat proměnné prostředí prostřednictvím [nastavení aplikace](./configure-common.md) a [připojovacích řetězců](./configure-common.md). Nastavení aplikace a připojovací řetězce se ukládají v Azure jako šifrované. Nastavení aplikace se dešifrují jenom předtím, než se vloží do paměti procesu vaší aplikace při spuštění aplikace. Šifrovací klíče se pravidelně otáčí. Alternativně můžete svou aplikaci Azure App Service integrovat s [Azure Key Vault](../key-vault/index.yml) pro pokročilou správu tajných klíčů. Když [přístup k Key Vault ke spravované identitě](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), vaše aplikace App Service může zabezpečeně přistupovat k tajným údajům, které potřebujete. |

## <a name="networking"></a>Sítě

| Doporučení | Komentáře |
|-|-|
| Použít omezení statických IP adres | Azure App Service ve Windows vám umožní definovat seznam IP adres, které mají povolený přístup k vaší aplikaci. Seznam povolených adres může zahrnovat jednotlivé IP adresy nebo rozsah IP adres definovaných maskou podsítě. Další informace najdete v tématu [Azure App Service omezení statických IP adres](app-service-ip-restrictions.md).  |
| Použití izolované cenové úrovně | S výjimkou cenové úrovně inizolace všechny úrovně spouštějí vaše aplikace na sdílené síťové infrastruktuře v Azure App Service. Izolovaná vrstva poskytuje kompletní izolaci sítě spuštěním aplikací v [prostředí vyhrazené App Service](environment/intro.md). Prostředí App Service běží ve vaší vlastní instanci [Azure Virtual Network](../virtual-network/index.yml).|
| Použití zabezpečených připojení při přístupu k místním prostředkům | Pomocí [hybridních připojení](app-service-hybrid-connections.md), [Virtual Network integrace](web-sites-integrate-with-vnet.md)nebo [App Service prostředí](environment/intro.md) se můžete připojit k místním prostředkům. |
| Omezit expozici příchozímu síťovému provozu | Skupiny zabezpečení sítě umožňují omezit přístup k síti a řídit počet vystavených koncových bodů. Další informace najdete v tématu [řízení příchozího provozu do App Service Environment](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře |
|-|-|
|Použít úroveň Azure Security Center úrovně Standard | [Azure Security Center](https://docs.microsoft.com/azure/security-center/defender-for-app-service-introduction) je nativně integrována s Azure App Service. Může spustit posouzení a poskytnout doporučení pro zabezpečení. |

## <a name="next-steps"></a>Další kroky

Obraťte se na poskytovatele aplikace a zjistěte, zda jsou k dispozici další požadavky na zabezpečení. Další informace o vývoji zabezpečených aplikací najdete v [dokumentaci k zabezpečení vývoje](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).
