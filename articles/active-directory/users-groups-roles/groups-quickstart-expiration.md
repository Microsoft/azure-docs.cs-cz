---
title: Rychlý Start zásad vypršení platnosti pro skupiny Office 365 – Azure Active Directory | Microsoft Docs
description: Vypršení platnosti u skupin Office 365 – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aabfe803a6ee40491dd95fe7def9a31838c80e65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72928977"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Rychlý start: Nastavení vypršení platnosti skupin Office 365 v Azure Active Directory

V tomto rychlém startu nastavíte zásadu vypršení platnosti pro skupiny Office 365. Když mají uživatelé možnost nastavovat vlastní skupiny, můžou se začít množit nepoužívané skupiny. Jedním ze způsobů, jak spravovat nepoužívané skupiny, je nastavit vypršení platnosti těchto skupin a tím snížit potřebu údržby související s ručním odstraňováním skupin.

Zásady vypršení platnosti je jednoduchá:

- Skupiny s aktivitami uživatele se automaticky Obnovují jako vypršení platnosti (Preview).
- Vlastníci skupiny obdrží oznámení o potřebě prodloužit platnost skupiny, které vyprší platnost.
- Skupina, které se neprodlouží platnost, se odstraní.
- Odstraněnou skupinu Office 365 může do 30 dnů obnovit vlastník skupiny nebo správce Azure AD.

> [!NOTE]
> Skupiny teď používají Azure AD Intelligence k automatickému obnovení na základě toho, jestli jsou v nedávném použití (aktuálně ve verzi Public Preview, takže se nevyžaduje žádné registraci). Tato funkce je založená na aktivitě uživatelů ve skupinách napříč službami Office 365, jako je Outlook, SharePoint, teams, Yammer a další.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisite"></a>Požadavek

 Minimální privilegovaná role požadovaná k nastavení vypršení platnosti skupiny je správcem uživatele v organizaci.

## <a name="turn-on-user-creation-for-groups"></a>Zapnutí vytváření uživatelů pro skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele.

2. Vyberte **Skupiny** a pak vyberte **Obecné**.
  
   ![Stránka nastavení samoobslužné skupiny](./media/groups-quickstart-expiration/self-service-settings.png)

3. Nastavte možnost **Uživatelé můžou vytvářet skupiny Office 365** na **Ano**.

4. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení skupin.

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com), vyberte **Azure Active Directory** > **skupiny** > **vypršení platnosti** a otevřete nastavení vypršení platnosti.
  
   ![Stránka nastavení vypršení platnosti pro skupinu](./media/groups-quickstart-expiration/expiration-settings.png)

2. Nastavte interval vypršení platnosti. Vyberte přednastavenou hodnotu nebo zadejte vlastní hodnotu vyšší než 31 dnů. 

3. Zadejte e-mailovou adresu, kam se mají odesílat oznámení o vypršení platnosti v případě, že skupina nemá žádného vlastníka.

4. Pro účely tohoto rychlého startu nastavte možnost **Povolit vypršení platnosti u těchto skupin Office 365** na **Všechny**.

5. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení vypršení platnosti.

A to je vše! V tomto rychlém startu jste úspěšně nastavili zásadu vypršení platnosti pro vybrané skupiny Office 365.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="to-remove-the-expiration-policy"></a>Odebrání zásad vypršení platnosti

1. Ujistěte se, že jste přihlášeni k webu [Azure Portal](https://portal.azure.com) pomocí účtu, který má k tenantovi oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **Skupiny** > **Vypršení platnosti**.
3. Nastavte možnost **Povolit vypršení platnosti u těchto skupin Office 365** na **Žádné**.

### <a name="to-turn-off-user-creation-for-groups"></a>Vypnutí vytváření uživatelů pro skupiny

1. Vyberte **Azure Active Directory** > **Skupiny** > **Obecné**. 
2. Nastavte možnost **Uživatelé můžou vytvářet skupiny Office 365 na webu Azure Portal** na **Ne**.

## <a name="next-steps"></a>Další kroky

Další informace o vypršení platnosti včetně pokynů PowerShellu a technických omezení najdete v následujícím článku:

> [!div class="nextstepaction"]
> [PowerShell – zásady vypršení platnosti](groups-lifecycle.md)
