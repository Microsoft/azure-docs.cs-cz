---
title: Rychlý start zásady vypršení platnosti pro skupiny Office 365 – Azure Active Directory | Dokumentace Microsoftu
description: Vypršení platnosti u skupin Office 365 – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0573448c753c763e818d641216033dbeacb9e9a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199303"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Rychlý start: Nastavení skupiny Office 365 do vypršení platnosti ve službě Azure Active Directory

V tomto rychlém startu nastavíte zásadu vypršení platnosti pro skupiny Office 365. Když mají uživatelé možnost nastavovat vlastní skupiny, můžou se začít množit nepoužívané skupiny. Jedním ze způsobů, jak spravovat nepoužívané skupiny, je nastavit vypršení platnosti těchto skupin a tím snížit potřebu údržby související s ručním odstraňováním skupin.

Zásady vypršení platnosti je jednoduchá:

* Vlastníci skupiny obdrží oznámení o potřebě prodloužit platnost skupiny, které vyprší platnost.
* Skupina, které se neprodlouží platnost, se odstraní.
* Odstraněnou skupinu Office 365 může do 30 dnů obnovit vlastník skupiny nebo správce Azure AD.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisite"></a>Požadavek

Musíte být globální správce nebo Správce uživatelů v organizaci nastavit vypršení platnosti skupiny.

## <a name="turn-on-user-creation-for-groups"></a>Zapnutí vytváření uživatelů pro skupiny

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu, který je globální správce nebo Správce uživatelů v organizaci.

2. Vyberte **Skupiny** a pak vyberte **Obecné**.
  
   ![Stránka nastavení samoobslužné skupiny](./media/groups-quickstart-expiration/self-service-settings.png)

3. Nastavte možnost **Uživatelé můžou vytvářet skupiny Office 365** na **Ano**.

4. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení skupin.

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)vyberte **Azure Active Directory** > **skupiny** > **vypršení platnosti** do Otevřete nastavení vypršení platnosti.
  
   ![Stránka nastavení vypršení platnosti pro skupiny](./media/groups-quickstart-expiration/expiration-settings.png)

2. Nastavte interval vypršení platnosti. Vyberte přednastavenou hodnotu nebo zadejte vlastní hodnotu vyšší než 31 dnů. 

3. Zadejte e-mailovou adresu, kam se mají odesílat oznámení o vypršení platnosti v případě, že skupina nemá žádného vlastníka.

4. Pro účely tohoto rychlého startu nastavte možnost **Povolit vypršení platnosti u těchto skupin Office 365** na **Všechny**.

5. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení vypršení platnosti.

A to je vše! V tomto rychlém startu jste úspěšně nastavili zásadu vypršení platnosti pro vybrané skupiny Office 365.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="to-remove-the-expiration-policy"></a>Chcete-li odebrat zásady vypršení platnosti

1. Ujistěte se, že jste přihlášeni k webu [Azure Portal](https://portal.azure.com) pomocí účtu, který má k tenantovi oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **Skupiny** > **Vypršení platnosti**.
3. Nastavte možnost **Povolit vypršení platnosti u těchto skupin Office 365** na **Žádné**.

### <a name="to-turn-off-user-creation-for-groups"></a>Chcete-li vypnout vytvoření uživatele pro skupiny

1. Vyberte **Azure Active Directory** > **Skupiny** > **Obecné**. 
2. Nastavte možnost **Uživatelé můžou vytvářet skupiny Office 365 na webu Azure Portal** na **Ne**.

## <a name="next-steps"></a>Další postup

Další informace o vypršení platnosti, včetně technických omezení, přidání seznamu vlastních blokovaných slov a prostředí pro koncové uživatele napříč aplikacemi Office 365 najdete v následujícím článku obsahujícím tyto podrobnosti o zásadách vypršení platnosti:

> [!div class="nextstepaction"]
> [Všechny podrobnosti o zásadách vypršení platnosti](groups-lifecycle.md)
