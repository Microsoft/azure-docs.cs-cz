---
title: Řešení běžných chyb ověřování | Azure Marketplace
description: Poskytuje pomoc s běžnými chybami ověřování při použití rozhraní portál partnerů cloudu API.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: bfcee6a6d9a8448315d59009ea8a6ef62fa71da0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535990"
---
# <a name="troubleshooting-common-authentication-errors"></a>Řešení běžných chyb ověřování

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Tento článek poskytuje pomoc s běžnými chybami ověřování při použití rozhraní portál partnerů cloudu API.

## <a name="unauthorized-error"></a>Neoprávněná chyba

Pokud se konzistentně zobrazí `401 unauthorized` chyby, ověřte, že máte platný přístupový token.  Pokud jste to ještě neudělali, vytvořte aplikaci Basic Azure Active Directory (Azure AD) a instanční objekt, jak je popsáno v tématu [použití portálu k vytvoření Azure Active Directory aplikace a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md). Pak pomocí aplikace nebo jednoduché žádosti HTTP POST ověřte přístup.  K získání přístupového tokenu, jak je znázorněno na následujícím obrázku, budete zahrnovat ID tenanta, ID aplikace, ID objektu a tajný klíč:

![Řešení chyby 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)

## <a name="forbidden-error"></a>Zakázaný přístup

Pokud se zobrazí `403 forbidden` Chyba, ujistěte se, že se do účtu vydavatele v portál partnerů cloudu přidal správný instanční objekt.
Podle pokynů na stránce [požadavky](./cloud-partner-portal-api-prerequisites.md) přidejte instanční objekt na portál.

Pokud byl přidán správný instanční objekt, ověřte všechny ostatní informace. Věnujte velkou pozornost na ID objektu zadané na portálu. Na stránce registrace aplikace Azure Active Directory jsou dvě ID objektů a musíte použít ID místního objektu. Správnou hodnotu můžete najít tak, že přejdete na stránku **Registrace aplikací** vaší aplikace a kliknete na název aplikace v části **spravovaná aplikace v místním adresáři**. Tím přejdete na místní vlastnosti aplikace, kde můžete najít správné ID objektu na stránce **vlastností** , jak je znázorněno na následujícím obrázku. Také se ujistěte, že při přidání instančního objektu a volání rozhraní API používáte správné ID vydavatele.

![Řešení chyby 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
