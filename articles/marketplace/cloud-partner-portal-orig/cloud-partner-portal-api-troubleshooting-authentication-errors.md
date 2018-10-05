---
title: Řešení potíží s běžnými chybami při ověřování | Dokumentace Microsoftu
description: Při použití rozhraní API portál Cloud Partner, poskytuje pomoc s běžnými chybami při ověřování.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 82a5ef86d1ca35cddb05cb4e126e64cc3759bcc0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809209"
---
<a name="troubleshooting-common-authentication-errors"></a>Řešení potíží s běžnými chybami při ověřování
------------------------------------------

Tento článek poskytuje pomoc s běžnými chybami při ověřování při použití rozhraní API portál partnerů cloudu.

### <a name="unauthorized-error"></a>Chyba Neautorizováno

Pokud se zobrazí konzistentně `401 unauthorized` chyby, ověřte, že máte platný přístupový token.  Pokud jste tak již neučinili, vytvořte základní aplikaci v Azure Active Directory (Azure AD) a instanční objekt služby podle popisu v [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Potom použijte k ověření přístupu aplikace nebo jednoduchého požadavku HTTP POST.  Bude obsahovat ID Tenanta, ID aplikace, ID objektu a tajného klíče, k získání přístupového tokenu, jak je znázorněno na následujícím obrázku:

![Řešení potíží s chyba 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


### <a name="forbidden-error"></a>Zakázaný přístup

Pokud se zobrazí `403 forbidden` chyba, ujistěte se, že správné instanční objekt je přidaný do svého vydavatelského účtu na portálu partnerů cloudu.
Postupujte podle kroků v [požadavky](./cloud-partner-portal-api-prerequisites.md) stránky a přidat váš objekt služby na portálu.

Pokud byl přidán správné instančního objektu, zkontrolujte všechny informace. Zavřít věnujte pozornost k ID objektu přešla na portálu. Na stránce registrace aplikace Azure Active Directory existují dva identifikátory ID objektu a je nutné použít místní ID objektu. Můžete najít správnou hodnotu tak, že přejdete **registrace aplikací** stránky pro vaše aplikace a kliknutím na název aplikace v rámci **spravované aplikace v místním adresáři**. Tím přejdete na místní vlastnosti na aplikaci, kde může najít správný Identifikátor objektu v **vlastnosti** stránce, jak je znázorněno na následujícím obrázku. Také se ujistěte, že používáte správný vydavatel ID při přidání objektu služby a provede volání rozhraní API.

![Řešení potíží s Chyba 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
