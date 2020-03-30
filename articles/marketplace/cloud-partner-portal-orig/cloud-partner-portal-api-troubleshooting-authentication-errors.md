---
title: Poradce při potížích s běžnými chybami ověřování | Azure Marketplace
description: Poskytuje pomoc s běžnými chybami ověřování při použití souborů API portálu cloudových partnerů.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 1da9bbd1ed4bc4abea0699e56d8adc397086d6e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280418"
---
# <a name="troubleshooting-common-authentication-errors"></a>Poradce při potížích s běžnými chybami ověřování

Tento článek poskytuje pomoc s běžnými chybami ověřování při použití souborů API portálu cloudových partnerů.

## <a name="unauthorized-error"></a>Neautorizovaná chyba

Pokud konzistentně získáte `401 unauthorized` chyby, ověřte, zda máte platný přístupový token.  Pokud jste tak ještě neučinili, vytvořte základní aplikaci Azure Active Directory (Azure AD) a instanční objekt, jak je popsáno v [části Použití portálu k vytvoření aplikace a instancí Azure Active Directory, která má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Potom použijte aplikaci nebo jednoduchý požadavek HTTP POST k ověření přístupu.  Zahrnete ID klienta, ID aplikace, ID objektu a tajný klíč k získání přístupového tokenu, jak je znázorněno na následujícím obrázku:

![Poradce při potížích s chybou 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Zakázaný přístup

Pokud se `403 forbidden` zobrazí chyba, ujistěte se, že správný instanční objekt služby byl přidán do vašeho účtu vydavatele na portálu cloudových partnerů.
Postupujte podle kroků na stránce [Požadavky](./cloud-partner-portal-api-prerequisites.md) a přidejte instanční objekt na portál.

Pokud byl přidán správný instanční objekt, ověřte všechny ostatní informace. Věnujte velkou pozornost ID objektu zadanému na portálu. Na stránce registrace aplikace Azure Active Directory jsou dvě ID objektů a musíte použít místní ID objektu. Správnou hodnotu najdete tak, že přejdete na stránku **Registrace aplikací** pro vaši aplikaci a kliknete na název aplikace v části **Spravovaná aplikace v místním adresáři**. Tím přejdete do místních vlastností aplikace, kde najdete správné ID objektu na stránce **Vlastnosti,** jak je znázorněno na následujícím obrázku. Také ujistěte se, že používáte správné ID vydavatele při přidání instančního objektu a provést volání rozhraní API.

![Poradce při potížích s chybou 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
