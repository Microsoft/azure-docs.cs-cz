---
title: Doporučení zabezpečení
description: Implementujte bezpečnostní doporučení, která vám pomohou splnit vaše bezpečnostní povinnosti, jak je uvedeno v našem modelu sdílené odpovědnosti. Zlepšete zabezpečení aplikace.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684113"
---
# <a name="security-recommendations-for-app-service"></a>Doporučení zabezpečení pro službu App Service

Tento článek obsahuje doporučení zabezpečení pro Azure App Service. Implementace těchto doporučení vám pomůže splnit vaše bezpečnostní povinnosti, jak je popsáno v našem modelu sdílené odpovědnosti, a zlepší celkové zabezpečení řešení webových aplikací. Další informace o tom, co Microsoft dělá pro plnění povinností poskytovatele služeb, načtěte načlánekl [zabezpečení infrastruktury Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Obecné

| Doporučení | Komentáře |
|-|-|----|
| Buďte v obraze | Používejte nejnovější verze podporovaných platforem, programovacích jazyků, protokolů a architektur. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře |
|-|----|
| Zakázání anonymního přístupu | Pokud nepotřebujete podporovat anonymní požadavky, zakažte anonymní přístup. Další informace o možnostech ověřování služby Azure App Service najdete [v tématu Ověřování a autorizace ve službě Azure App Service](overview-authentication-authorization.md).|
| Vyžadovat ověření | Kdykoli je to možné, použijte ověřovací modul služby App Service namísto psaní kódu pro zpracování ověřování a autorizace. Viz [Ověřování a autorizace ve službě Azure App Service](overview-authentication-authorization.md). |
| Ochrana back-endových prostředků pomocí ověřeného přístupu | Můžete použít identitu uživatele nebo použít identitu aplikace k ověření na prostředek back-end. Pokud se rozhodnete použít identitu aplikace, použijte [spravovanou identitu](overview-managed-identity.md).
| Vyžadovat ověření klientského certifikátu | Ověřování klientských certifikátů zlepšuje zabezpečení tím, že umožňuje pouze připojení od klientů, kteří mohou ověřovat pomocí certifikátů, které zadáte. |

## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře |
|-|-|
| Přesměrovat http na https | Ve výchozím nastavení se klienti mohou připojit k webovým aplikacím pomocí protokolu HTTP nebo protokolu HTTPS. Doporučujeme přesměrovat protokol HTTP na protokol HTTP, protože protokol HTTPS používá protokol SSL/TLS k zajištění zabezpečeného připojení, které je šifrované i ověřené. |
| Šifrování komunikace s prostředky Azure | Když se vaše aplikace připojí k prostředkům Azure, jako je [SQL Database](https://azure.microsoft.com/services/sql-database/) nebo [Azure Storage](/azure/storage/), připojení zůstane v Azure. Vzhledem k tomu, že připojení prochází sdílené sítě v Azure, měli byste vždy šifrovat veškerou komunikaci. |
| Vyžadovat možnou nejnovější verzi TLS | Od roku 2018 používají nové aplikace Služby Azure App Service TLS 1.2. Novější verze tls zahrnují vylepšení zabezpečení oproti starším verzím protokolu. |
| Použití FTPS | Služba App Service podporuje ftp i FTPS pro nasazení souborů. Pokud je to možné, použijte místo FTP místo FTP. Pokud jeden nebo oba tyto protokoly nejsou používány, měli byste [je zakázat](deploy-ftp.md#enforce-ftps). |
| Zabezpečení dat aplikací | Neukládejte tajné klíče aplikací, jako jsou přihlašovací údaje databáze, tokeny rozhraní API nebo soukromé klíče ve vašem kódu nebo konfiguračních souborech. Běžně přijímaný přístup je přístup k nim jako [proměnné prostředí](https://wikipedia.org/wiki/Environment_variable) pomocí standardní vzor ve vašem jazyce volby. Ve službě Azure App Service můžete definovat proměnné prostředí prostřednictvím [nastavení aplikací](web-sites-configure.md) a [připojovacích řetězců](web-sites-configure.md). Nastavení aplikací a připojovací řetězce se ukládají šifrované v Azure. Nastavení aplikace se dešifruje pouze před vložením do procesní paměti aplikace při spuštění aplikace. Šifrovací klíče se pravidelně otáčejí. Případně můžete integrovat aplikaci Azure App Service s [Azure Key Vault](/azure/key-vault/) pro pokročilou správu tajných klíčů. [Přístup k trezoru klíčů se spravovanou identitou](../key-vault/tutorial-web-application-keyvault.md)může vaše aplikace App Service bezpečně přistupovat k tajným klíčům, které potřebujete. |

## <a name="networking"></a>Síťové služby

| Doporučení | Komentáře |
|-|-|
| Použití statických omezení IP | Azure App Service ve Windows umožňuje definovat seznam IP adres, které mají povolený přístup k vaší aplikaci. Povolený seznam může obsahovat jednotlivé adresy IP nebo rozsah adres IP definovaných maskou podsítě. Další informace najdete v [tématu Azure App Service Statická omezení IP](app-service-ip-restrictions.md).  |
| Použití izolované cenové úrovně | S výjimkou izolované cenové úrovně spouštějí všechny úrovně vaše aplikace na sdílené síťové infrastruktuře ve službě Azure App Service. Izolovaná vrstva poskytuje úplnou izolaci sítě spuštěním aplikací v prostředí vyhrazené [služby App Service](environment/intro.md). Prostředí služby App Service běží ve vaší vlastní instanci [virtuální sítě Azure](/azure/virtual-network/).|
| Použití zabezpečených připojení při přístupu k místním prostředkům | K připojení k místním prostředkům můžete použít [hybridní připojení](app-service-hybrid-connections.md), integraci [virtuální sítě](web-sites-integrate-with-vnet.md)nebo [prostředí služby App Service.](environment/intro.md) |
| Omezit expozici příchozímu síťovému provozu | Skupiny zabezpečení sítě umožňují omezit přístup k síti a řídit počet exponovaných koncových bodů. Další informace naleznete v tématu [Jak řídit příchozí provoz do prostředí služby App Service](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře |
|-|-|
|Použití standardní úrovně Azure Security Center | [Azure Security Center](../security-center/security-center-app-services.md) je nativně integrované se službou Azure App Service. Může spouštět hodnocení a poskytovat doporučení zabezpečení. |

## <a name="next-steps"></a>Další kroky

Obraťte se na svého poskytovatele aplikace a zjistěte, zda existují další požadavky na zabezpečení. Další informace o vývoji zabezpečených aplikací naleznete v [tématu Bezpečná vývojová dokumentace](../security/fundamentals/abstract-develop-secure-apps.md).
