---
title: Vyřešit chybu přihlášení pro vývojářské nástroje Azure pro vzdělávání
description: V této části najdete popis akcí, které by student měl provést, pokud při přihlašování k vývojářským nástrojům Azure pro vzdělávání dostanou chybovou zprávu.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096650"
---
# <a name="troubleshooting-student-login-issues"></a>Řešení potíží s přihlášením studentů
Přístup k vývojářským nástrojům Azure pro vzdělávání vyžaduje, aby uživatel měl účet Microsoft (MSA). Studenti budou automaticky přesměrováni, aby vytvořili MSA, pokud jejich účet ještě není MSA nebo propojený s MSA. Pokud je vaše doména přidružená ke službě Active Directory, všechny účty v této doméně se už považují za MSA.

Pokud se student pokusí přihlásit a obdrží následující chybovou zprávu, použijte jedno z řešení popsaných níže.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Chybová zpráva přihlášení" border="false":::

Existují dvě řešení: Vytvořte novou účet Microsoft nebo použijte existující účet Microsoft.

## <a name="create-a-new-microsoft-account"></a>Vytvořit nový účet Microsoft
### <a name="use-a-university-email-address"></a>Použití e-mailové adresy univerzity
Pokud se přihlašujete pomocí e-mailové adresy univerzity (například `John.Smith@university.edu` ), budete muset vytvořit účet Microsoft s touto e-mailovou adresou. Vytvoření účtu je bezplatné a trvá jenom několik minut. Účet Microsoft vám umožní automaticky se přihlásit ke všem produktům Microsoftu pomocí jediného uživatelského jména a hesla.

### <a name="use-a-personal-email-address"></a>Použít osobní e-mailovou adresu
Pokud se přihlašujete pomocí osobní e-mailové adresy (například `John.Smith@email.com` ), ale má taky e-mailovou adresu univerzity, zkuste použít e-mailovou adresu univerzity. Pokud jste se dříve přihlásili do webstoru vaší instituce pomocí osobní e-mailové adresy nebo nemáte e-mailovou adresu univerzity, budete muset účet Microsoft vytvořit nebo propojit s vaší osobní e-mailovou adresou.

## <a name="use-an-existing-microsoft-account"></a>Použít existující účet Microsoft
Pokud má student existující účet Microsoft (například Xbox), může tento účet připojit k účtu Azure dev Tools.

1. Přejděte na https://account.microsoft.com.
1. Přihlaste se pomocí přihlašovacích údajů účet Microsoft.
1. V nabídce horní pás karet vyberte **vaše informace** .

1. Klikněte na **spravovat způsob přihlášení k Microsoftu**. Zobrazí se výzva k ověření vaší identity. Pošleme vám e-mail bezpečnostní kód.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Spravovat přihlášení" border="false":::

1. Zadejte e-mailový kód zabezpečení.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Zadejte kód pro přihlášení." border="false":::!

1. Klikněte na **Přidat e-maily** na svůj účet a zadejte svoji e-mailovou adresu školy.
Až se příště přihlásíte, můžete použít vaši e-mailovou adresu vysoké školy pro přístup k vývojářským nástrojům Azure pro vývoj.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Spravujte, jak se přihlašujete k Microsoftu." border="false":::

## <a name="next-steps"></a>Další kroky
- [Nejčastější dotazy](program-faq.md)

- [Možnosti podpory](program-support.md)
