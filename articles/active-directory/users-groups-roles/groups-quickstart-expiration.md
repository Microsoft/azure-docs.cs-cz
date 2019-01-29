---
title: Rychlý start k zásadám vypršení platnosti pro skupiny Office 365 v Azure Active Directory | Microsoft Docs
description: Vypršení platnosti u skupin Office 365 – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 6d2b5201c41ba9d5c849976f0227e9abadea7658
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165230"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Rychlý start: Nastavení skupiny Office 365 do vypršení platnosti ve službě Azure Active Directory

V tomto rychlém startu nastavíte zásadu vypršení platnosti pro skupiny Office 365. Když mají uživatelé možnost nastavovat vlastní skupiny, můžou se začít množit nepoužívané skupiny. Jedním ze způsobů, jak spravovat nepoužívané skupiny, je nastavit vypršení platnosti těchto skupin a tím snížit potřebu údržby související s ručním odstraňováním skupin.

Zásady vypršení platnosti je jednoduchá:

* Vlastníci skupiny obdrží oznámení o potřebě prodloužit platnost skupiny, které vyprší platnost.
* Skupina, které se neprodlouží platnost, se odstraní.
* Odstraněnou skupinu Office 365 může do 30 dnů obnovit vlastník skupiny nebo správce Azure AD.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisite"></a>Požadavek

Pokud chcete nastavit vypršení platnosti skupiny, musíte v tenantovi být globálním správcem nebo správcem uživatelských účtů.

## <a name="turn-on-user-creation-for-groups"></a>Zapnutí vytváření uživatelů pro skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce nebo správce uživatelských účtů.

2. Vyberte **Skupiny** a pak vyberte **Obecné**.
  
  ![Samoobslužné nastavení skupin](./media/groups-quickstart-expiration/self-service-settings.png)

3. Nastavte možnost **Uživatelé můžou vytvářet skupiny Office 365** na **Ano**.

4. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení skupin.

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory** > **Skupiny** > **Vypršení platnosti** a otevřete nastavení vypršení platnosti.
  
  ![Nastavení vypršení platnosti](./media/groups-quickstart-expiration/expiration-settings.png)

2. Nastavte interval vypršení platnosti. Vyberte přednastavenou hodnotu nebo zadejte vlastní hodnotu vyšší než 31 dnů. 

3. Zadejte e-mailovou adresu, kam se mají odesílat oznámení o vypršení platnosti v případě, že skupina nemá žádného vlastníka.

4. Pro účely tohoto rychlého startu nastavte možnost **Povolit vypršení platnosti u těchto skupin Office 365** na **Všechny**.

5. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení vypršení platnosti.

A to je vše! V tomto rychlém startu jste úspěšně nastavili zásadu vypršení platnosti pro vybrané skupiny Office 365.

## <a name="clean-up-resources"></a>Vyčištění prostředků

**Odebrání zásady vypršení platnosti**

1. Ujistěte se, že jste přihlášeni k webu [Azure Portal](https://portal.azure.com) pomocí účtu, který má k tenantovi oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **Skupiny** > **Vypršení platnosti**.
3. Nastavte možnost **Povolit vypršení platnosti u těchto skupin Office 365** na **Žádné**.

**Vypnutí vytváření uživatelů pro skupiny**

1. Vyberte **Azure Active Directory** > **Skupiny** > **Obecné**. 
2. Nastavte možnost **Uživatelé můžou vytvářet skupiny Office 365 na webu Azure Portal** na **Ne**.

## <a name="next-steps"></a>Další postup

Další informace o vypršení platnosti, včetně technických omezení, přidání seznamu vlastních blokovaných slov a prostředí pro koncové uživatele napříč aplikacemi Office 365 najdete v následujícím článku obsahujícím tyto podrobnosti o zásadách vypršení platnosti:

> [!div class="nextstepaction"]
> [Všechny podrobnosti o zásadách vypršení platnosti](groups-lifecycle.md)
