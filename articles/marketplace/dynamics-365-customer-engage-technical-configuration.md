---
title: Nastavení Dynamics 365 pro zákaznickou zapojení & PowerApps na Microsoft AppSource (Azure Marketplace)
description: Nastavení Dynamics 365 pro zákaznickou zapojení & PowerApps nabízí technickou konfiguraci na Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820358"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Nastavení Dynamics 365 pro zákaznickou zapojení & Power Apps nabízí technickou konfiguraci

Tato stránka definuje technické podrobnosti používané pro připojení k vaší nabídce. Toto připojení nám umožní zřídit vaši nabídku pro koncového zákazníka, pokud se k tomu rozhodne získat.

## <a name="offer-information"></a>Informace o nabídce

**Základní licenční model** určuje, jak se zákazníkům přiřadí vaše aplikace v centru pro správu CRM. Vyberte **prostředek** pro licencování na základě instance nebo **uživatele** , pokud jsou licence přiřazeny k jednomu klientovi.

Zaškrtávací políčko **vyžaduje přístup k odchozímu přístupu S2S a přístup k zabezpečenému ÚLOŽIŠTI CRM** umožňuje konfiguraci odchozího přístupu typu Server-Server (S2S) pro zabezpečení CRM. Tato funkce vyžaduje zvláštní pozornost od týmu Dynamics 365 během fáze certifikace. Společnost Microsoft vás bude kontaktovat, abyste mohli provést další kroky pro podporu této funkce.

Ponechte **adresu URL konfigurace aplikace** prázdnou; je pro budoucí použití.

## <a name="crm-package"></a>Balíček CRM

V poli **Adresa URL umístění balíčku** zadejte adresu URL účtu Azure Blob Storage, který obsahuje nahraný soubor. zip balíčku CRM. Zahrňte do adresy URL klíč SAS, který je jen pro čtení, aby Microsoft mohl vyzvednutí vašeho balíčku na ověření.

> [!IMPORTANT]
> Aby se zabránilo publikačnímu bloku, ujistěte se, že platnost data vypršení platnosti v adrese URL úložiště objektů BLOB vypršela. Datum můžete upravit přístupem k zásadám. **Doba vypršení platnosti** doporučujeme v budoucnu aspoň jeden měsíc.

V případě potřeby vyberte v seznamu **soubor balíčku více než jeden balíček CRM** . Pokud ano, nezapomeňte zahrnout všechny balíčky v souboru. zip.

Podrobné informace o tom, jak sestavit balíček a aktualizovat jeho strukturu, najdete v [kroku 3: vytvoření balíčku AppSource pro vaši aplikaci](/powerapps/developer/common-data-service/create-package-app-appsource).

## <a name="crm-package-availability"></a>Dostupnost balíčku CRM

Vyberte **+ Přidat oblast** a určete geografické oblasti, ve kterých bude mít váš balíček CRM k dispozici pro zákazníky. Nasazení do následujících oblastí svrchovaného přístupu vyžaduje speciální oprávnění a ověření během certifikace: [Německo](../germany/index.yml), [Cloud pro státní správu USA](../azure-government/documentation-government-welcome.md)a Tip.

Ve výchozím nastavení se **Adresa URL konfigurace aplikace** , kterou jste zadali výše, použije pro každou oblast. Pokud dáváte přednost, můžete zadat samostatnou adresu URL konfigurace aplikace pro jednu nebo více konkrétních oblastí.

Než budete pokračovat na další kartu v nabídce vlevo, vyberte možnost **Uložit koncept** , **společně s Microsoftem proveďte prodej**. Informace o tom, jak nastavit společný prodej s Microsoftem (volitelné), najdete v tématu [spoluprodejní účast partnerů](marketplace-co-sell.md). Pokud nenastavíte společný prodej nebo jste hotovi, pokračujte dalšími **kroky** uvedenými níže.

## <a name="next-steps"></a>Další kroky

- [Konfigurace doplňkového obsahu](dynamics-365-customer-engage-supplemental-content.md)
