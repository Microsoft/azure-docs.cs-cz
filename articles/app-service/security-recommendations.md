---
title: Doporučení zabezpečení pro službu Azure App Service
description: 'Doporučení zabezpečení pro službu Azure App Service. Implementace těchto doporučení zlepšíte celkové zabezpečení vašich řešení webové aplikace nápovědu, jak je popsáno v našich sdílená odpovědnost modelu a bude se splnit vaše povinnosti: zabezpečení.'
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.openlocfilehash: f50532a21d11d2f7142fa3ee7ed08f759df36d52
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207538"
---
# <a name="security-recommendations-for-app-service"></a>Doporučení zabezpečení pro službu App Service

Tento článek obsahuje doporučení týkající se zabezpečení pro službu Azure App Service. Implementace těchto doporučení zlepšíte celkové zabezpečení vašich řešení webové aplikace nápovědu, jak je popsáno v našich sdílená odpovědnost modelu a bude se splnit vaše povinnosti: zabezpečení. Další informace o jaké Microsoft provádí za účelem splnění odpovědnost poskytovatel služby, přečtěte si [zabezpečení infrastruktury Azure](../security/azure-security-infrastructure.md).

## <a name="general"></a>Obecné

| Doporučení | Komentáře |
|-|-|----|
| Udržujte si přehled | Použijte nejnovější verze sady podporované platformy, programovacích jazyků, protokoly a rozhraní. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře |
|-|----|
| Zakázat anonymní přístup | Pokud potřebujete anonymní žádosti o podporu, zakážete anonymní přístup. Další informace o možnostech ověřování služby Azure App Service najdete v tématu [ověřování a autorizace ve službě Azure App Service](overview-authentication-authorization.md).|
| Vyžadovat ověření | Kdykoli je to možné, použijte ověřovací modul služby App Service místo psaní kódu pro zpracování ověřování a autorizace. Zobrazit [ověřování a autorizace ve službě Azure App Service](overview-authentication-authorization.md). |
| Ochrana prostředků back-end s ověřený přístup | Můžete buď používat identitu uživatele nebo použijte identity aplikací k ověření na prostředek back-end. Při výběru pomocí identity aplikace [se identita spravované](overview-managed-identity.md).
| Vyžadovat ověření klientského certifikátu | Ověřování klientským certifikátem zvyšuje zabezpečení tím, že pouze připojení od klientů, kteří můžou ověřovat pomocí certifikátů, které zadáte. |

## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře |
|-|-|
| Přesměrování HTTP na HTTPs | Ve výchozím nastavení klienti mohou připojit k webovým aplikacím pomocí protokolu HTTP nebo HTTPS. Doporučujeme, abyste přesměrování HTTP na HTTPs, protože protokol HTTPS používá protokol SSL/TLS a nabízí tak zabezpečené připojení je šifrovaný a ověření. |
| Šifrování komunikace s prostředky Azure | Když vaše aplikace se připojí k prostředky Azure, jako například [SQL Database](https://azure.microsoft.com/services/sql-database/) nebo [služby Azure Storage](/azure/storage/), připojení zůstane v Azure. Vzhledem k tomu, že připojení prochází sdílené sítě v systému Azure, by měla vždy šifrování veškeré komunikace. |
| Vyžaduje nejnovější verzi TLS, je to možné | Protože 2018 nové aplikace služby Azure App Service pomocí protokolu TLS 1.2. Novější verze TLS zahrnují vylepšení zabezpečení oproti starší verze protokolu. |
| Pomocí FTPS | App Service podporuje pro nasazování souborů protokolu FTP a FTPS. Pomocí FTPS místo FTP, pokud je to možné. Pokud jeden nebo oba tyto protokoly nejsou používány, měli byste [je zakázat](deploy-ftp.md#enforce-ftps). |
| Zabezpečení dat aplikací | Neukládejte tajných klíčů aplikací, jako je například přihlašovací údaje databáze, tokeny rozhraní API nebo privátní klíče do kódu nebo konfiguračního souboru. Obecně uznávaný přístup je pro přístup k nim jako [proměnné prostředí](https://wikipedia.org/wiki/Environment_variable) standardním způsobem v jazyce podle výběru. Ve službě Azure App Service, můžete definovat proměnné prostředí prostřednictvím [nastavení aplikace](web-sites-configure.md) a [připojovací řetězce](web-sites-configure.md). Nastavení aplikace a připojovacích řetězců jsou uloženy v zašifrované podobě v Azure. Nastavení aplikace se dešifrují jenom před se vloží do paměti procesu vaší aplikace při spuštění aplikace. Šifrovací klíče jsou pravidelně otočen. Alternativně můžete integrovat aplikace služby Azure App Service s využitím [Azure Key Vault](/azure/key-vault/) pro správu rozšířené tajných kódů. Podle [přístup k trezoru klíčů na spravovanou identitu](../key-vault/tutorial-web-application-keyvault.md), vaše aplikace app Service mít bezpečný přístup, tajné kódy, které potřebujete. |

## <a name="networking"></a>Sítě

| Doporučení | Komentáře |
|-|-|
| Používat omezení statických IP adres | Azure App Service ve Windows umožňuje definovat seznam IP adres, které můžou přistupovat k vaší aplikace. Seznam povolených položek může obsahovat jednotlivé IP adresy nebo rozsah IP adres určené maskou podsítě. Další informace najdete v tématu [Azure App Service statické omezení IP adres](app-service-ip-restrictions.md).  |
| Použití izolované cenové úrovně | S výjimkou izolované cenové úrovně u všech úrovní aplikace můžete spouštět ve sdílené síťové infrastruktury ve službě Azure App Service. Izolované úroveň poskytuje kompletní síťovou izolaci spuštěním vaší aplikace v rámci vyhrazeného [App Service environment](environment/intro.md). Služby App Service environment běží ve vlastní instanci [Azure Virtual Network](/azure/virtual-network/).|
| Použít zabezpečené připojení při přístupu k místním prostředkům | Můžete použít [Hybrid connections](app-service-hybrid-connections.md), [integrace služby Virtual Network](web-sites-integrate-with-vnet.md), nebo [App Service environment](environment/intro.md) pro připojení k místním prostředkům. |
| Omezit vystavení pro příchozí síťový provoz | Skupiny zabezpečení sítě umožňují omezení síťového přístupu a řízení počtu zveřejněných koncových bodů. Další informace najdete v tématu [jak k řízení příchozího provozu do služby App Service Environment](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře |
|-|-|
|Použití Azure Security Center úrovně standard | [Azure Security Center](../security-center/security-center-app-services.md) je nativně integrováno s Azure App Service. Může spustit posouzení a poskytovat doporučení týkající se zabezpečení. |

## <a name="next-steps"></a>Další postup

Obraťte se na svého poskytovatele aplikace, pokud existují další požadavky na zabezpečení. Další informace o vývoji bezpečných aplikací najdete v tématu [zabezpečené dokumentace k vývoji](../security/abstract-develop-secure-apps.md).
