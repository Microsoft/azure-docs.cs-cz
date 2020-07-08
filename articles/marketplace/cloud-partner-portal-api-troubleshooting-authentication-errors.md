---
title: Řešení běžných chyb ověřování | Azure Marketplace
description: Poskytuje pomoc s běžnými chybami ověřování při použití rozhraní portál partnerů cloudu API.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 7684e097118fce4dcd471257ec0d9d476ce342be
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964919"
---
# <a name="troubleshooting-common-authentication-errors"></a>Řešení běžných chyb ověřování

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i po migraci nabídek do partnerského centra. Integrace přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že váš kód bude i nadále fungovat po migraci do partnerského centra.

Tento článek poskytuje pomoc s běžnými chybami ověřování při použití rozhraní portál partnerů cloudu API.

## <a name="unauthorized-error"></a>Neoprávněná chyba

Pokud se konzistentně zobrazí `401 unauthorized` chyby, ověřte, že máte platný přístupový token.  Pokud jste to ještě neudělali, vytvořte aplikaci Basic Azure Active Directory (Azure AD) a instanční objekt, jak je popsáno v tématu [použití portálu k vytvoření Azure Active Directory aplikace a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md). Pak pomocí aplikace nebo jednoduché žádosti HTTP POST ověřte přístup.  K získání přístupového tokenu, jak je znázorněno na následujícím obrázku, budete zahrnovat ID tenanta, ID aplikace, ID objektu a tajný klíč:

![Řešení chyby 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Zakázaný přístup

Pokud se zobrazí `403 forbidden` Chyba, ujistěte se, že se do účtu vydavatele v portál partnerů cloudu přidal správný instanční objekt.
Podle pokynů na stránce [požadavky](./cloud-partner-portal-api-prerequisites.md) přidejte instanční objekt na portál.

Pokud byl přidán správný instanční objekt, ověřte všechny ostatní informace. Věnujte velkou pozornost na ID objektu zadané na portálu. Na stránce registrace aplikace Azure Active Directory jsou dvě ID objektů a musíte použít ID místního objektu. Správnou hodnotu můžete najít tak, že přejdete na stránku **Registrace aplikací** vaší aplikace a kliknete na název aplikace v části **spravovaná aplikace v místním adresáři**. Tím přejdete na místní vlastnosti aplikace, kde můžete najít správné ID objektu na stránce **vlastností** , jak je znázorněno na následujícím obrázku. Také se ujistěte, že při přidání instančního objektu a volání rozhraní API používáte správné ID vydavatele.

![Řešení chyby 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
