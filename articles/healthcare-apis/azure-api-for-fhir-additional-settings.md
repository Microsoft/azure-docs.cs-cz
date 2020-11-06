---
title: Další nastavení pro Azure API pro FHIR
description: Přehled dalších nastavení, která můžete nastavit pro Azure API pro FHIR
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: b9949ab4cfa42553e5a8d28244b8f621f09334d1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398160"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Další nastavení pro Azure API pro FHIR

V tomto průvodci si projdeme další nastavení, která můžete chtít nastavit v rozhraní API Azure pro FHIR. K dispozici jsou další stránky, které vám pomohou podrobněji zobrazit další podrobnosti.

## <a name="configure-database-settings"></a>Konfigurovat nastavení databáze

Azure API pro FHIR používá k ukládání dat databázi. Výkon podkladové databáze závisí na počtu jednotek žádosti (RU) vybraných během zřizování služby nebo v nastavení databáze po zřízení služby.

Aby bylo zajištěno, že pro vaši databázi jsou neustále k dispozici dostatečné systémové prostředky, musí být zajištěna propustnost. Kolik ru potřebujete pro vaši aplikaci, závisí na operacích, které provádíte. Operace mohou být v rozsahu od jednoduchých čtení a zápisu až po složitější dotazy.

Další informace o tom, jak změnit výchozí nastavení, najdete v tématu [Konfigurace nastavení databáze](configure-database.md).

## <a name="access-control"></a>Řízení přístupu

Rozhraní Azure API pro FHIR umožňuje autorizovaným uživatelům přistoupit k rozhraní FHIR API. Autorizované uživatele můžete nakonfigurovat pomocí dvou různých mechanismů. Primární a doporučený způsob, jak nakonfigurovat řízení přístupu, je použití [řízení přístupu na základě role Azure (RBAC)](../role-based-access-control/index.yml), které je přístupné prostřednictvím okna **řízení přístupu (IAM)** . Azure RBAC funguje jenom v případě, že chcete zabezpečit přístup k rovině dat pomocí klienta Azure Active Directory přidruženého k vašemu předplatnému. Pokud chcete použít jiného tenanta, rozhraní API Azure pro FHIR nabízí mechanismus řízení přístupu místní roviny dat FHIR. Při použití místního mechanismu RBAC nejsou možnosti konfigurace tak bohatě formátované. Pro podrobnosti vyberte jednu z následujících možností:

* [Azure RBAC pro rovinu dat FHIR](configure-azure-rbac.md) Tato možnost je upřednostňovaná, když používáte klienta Azure Active Directory přidruženého k vašemu předplatnému.
* [Řízení přístupu k rovině dat místních FHIR](configure-local-rbac.md). Tuto možnost použijte pouze v případě, že pro řízení přístupu k rovině dat potřebujete použít externího tenanta Azure Active Directory. 

## <a name="enable-diagnostic-logging"></a>Povolení protokolování diagnostiky
V rámci instalace může být vhodné povolit protokolování diagnostiky, aby bylo možné monitorovat vaši službu a měly přesné hlášení pro účely dodržování předpisů. Podrobnosti o tom, jak nastavit protokolování diagnostiky, najdete v našem [Průvodci](enable-diagnostic-logging.md) , jak nastavit diagnostické protokolování, spolu s některými ukázkovými dotazy. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Použití vlastních hlaviček k přidání dat do protokolů auditu
V rozhraní API Azure pro FHIR možná budete chtít zahrnout do protokolů Další informace, které pocházejí z volajícího systému. Pokud chcete tyto informace zahrnout, můžete použít vlastní hlavičky.

Vlastní hlavičky můžete použít k zachycení několika typů informací. Například:

* Informace o identitě nebo autorizaci
* Původ volajícího
* Původní organizace
* Podrobnosti o klientském systému (elektronický záznam o stavu, portál pacient)

Pokud chcete tato data přidat do protokolů auditu, přečtěte si článek [použití vlastních hlaviček protokolu HTTP k přidávání dat do protokolů auditu](use-custom-headers.md) .

## <a name="next-steps"></a>Další kroky

V této příručce se naučíte nastavit další nastavení pro Azure API pro FHIR.

Dále si Projděte řadu kurzů pro vytvoření webové aplikace, která čte FHIR data.

>[!div class="nextstepaction"]
>[Nasazení aplikace JavaScriptu](tutorial-web-app-fhir-server.md)