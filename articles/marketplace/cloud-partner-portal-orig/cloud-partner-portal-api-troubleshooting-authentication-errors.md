---
title: Řešení potíží s běžnými chybami při ověřování | Azure Marketplace
description: Při použití rozhraní API portál Cloud Partner, poskytuje pomoc s běžnými chybami při ověřování.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ddf3c9ce26a1538d91f1e6d6bcc04fd0d18e7936
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935807"
---
# <a name="troubleshooting-common-authentication-errors"></a>Řešení potíží s běžnými chybami při ověřování

Tento článek poskytuje pomoc s běžnými chybami při ověřování při použití rozhraní API portál partnerů cloudu.

## <a name="unauthorized-error"></a>Chyba Neautorizováno

Pokud se zobrazí konzistentně `401 unauthorized` chyby, ověřte, že máte platný přístupový token.  Pokud jste tak již neučinili, vytvořte základní aplikaci v Azure Active Directory (Azure AD) a instanční objekt služby podle popisu v [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Potom použijte k ověření přístupu aplikace nebo jednoduchého požadavku HTTP POST.  Bude obsahovat ID Tenanta, ID aplikace, ID objektu a tajného klíče, k získání přístupového tokenu, jak je znázorněno na následujícím obrázku:

![Řešení potíží s chyba 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Zakázaný přístup

Pokud se zobrazí `403 forbidden` chyba, ujistěte se, že správné instanční objekt je přidaný do svého vydavatelského účtu na portálu partnerů cloudu.
Postupujte podle kroků v [požadavky](./cloud-partner-portal-api-prerequisites.md) stránky a přidat váš objekt služby na portálu.

Pokud byl přidán správné instančního objektu, zkontrolujte všechny informace. Zavřít věnujte pozornost k ID objektu přešla na portálu. Na stránce registrace aplikace Azure Active Directory existují dva identifikátory ID objektu a je nutné použít místní ID objektu. Můžete najít správnou hodnotu tak, že přejdete **registrace aplikací** stránky pro vaše aplikace a kliknutím na název aplikace v rámci **spravované aplikace v místním adresáři**. Tím přejdete na místní vlastnosti na aplikaci, kde může najít správný Identifikátor objektu v **vlastnosti** stránce, jak je znázorněno na následujícím obrázku. Také se ujistěte, že používáte správný vydavatel ID při přidání objektu služby a provede volání rozhraní API.

![Řešení potíží s Chyba 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
