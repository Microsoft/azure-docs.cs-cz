---
title: Řešení potíží s analýzou změn aplikace – Azure Monitor
description: Naučte se řešit problémy s analýzou změn aplikací.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100520968"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Řešení potíží s analýzou změn aplikace (Preview)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Máte potíže s registrací Microsoftu. Změna poskytovatele prostředků analýzy z karty historie změn

Pokud při prvním zobrazení historie změn po integraci s analýzou změn aplikace vidíte historii změn, uvidíte, že se automaticky zaregistruje poskytovatel prostředků **Microsoft. ChangeAnalysis**. Ve výjimečných případech může selhání selhat z následujících důvodů:

- Nemáte **dostatečná oprávnění k registraci poskytovatele prostředků Microsoft. ChangeAnalysis**. Tato chybová zpráva znamená, že vaše role v aktuálním předplatném nemá přiřazený obor **Microsoft. support/registr/Action** . K tomu může dojít v případě, že nejste vlastníkem předplatného a máte oprávnění ke sdíleným přístupům prostřednictvím spolupracovníka (tj. zobrazení přístupu ke skupině prostředků). Chcete-li tento problém vyřešit, obraťte se na vlastníka předplatného, abyste mohli zaregistrovat poskytovatele prostředků **Microsoft. ChangeAnalysis** . To se dá udělat v Azure Portal prostřednictvím **předplatných | Poskytovatelé prostředků** a vyhledejte ```Microsoft.ChangeAnalysis``` a zaregistrujte se v uživatelském rozhraní nebo prostřednictvím Azure PowerShell nebo rozhraní příkazového řádku Azure.

    Registrovat poskytovatele prostředků prostřednictvím PowerShellu:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Nepovedlo se zaregistrovat poskytovatele prostředků Microsoft. ChangeAnalysis**. Tato zpráva znamená, že se něco nezdařilo hned po odeslání žádosti o registraci poskytovatele prostředků do uživatelského rozhraní a není v souvislosti s problémem s oprávněním. Pravděpodobně se může jednat o dočasný problém s připojením k Internetu. Zkuste aktualizovat stránku a zkontrolovat připojení k Internetu. Pokud chyba přetrvává, obraťte se na changeanalysishelp@microsoft.com

- **Trvá to déle, než se čekalo**. Tato zpráva znamená, že registrace trvá déle než 2 minuty. To je neobvyklé, ale nemusí nutně znamenat, že se něco pokazilo. Můžete přejít k **předplatným | Poskytovatel prostředků** pro kontrolu stavu registrace poskytovatele prostředků **Microsoft. ChangeAnalysis** . Můžete zkusit použít uživatelské rozhraní pro zrušení registrace, opětovného registraci nebo obnovení, abyste viděli, jestli pomáhá. Pokud se problém opakuje, obraťte se changeanalysishelp@microsoft.com na podporu.
    ![Řešení potíží s registrací RP trvá příliš dlouho](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Snímek obrazovky nástroje pro diagnostiku a řešení problémů pro virtuální počítač s vybranými nástroji pro řešení potíží](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Snímek obrazovky dlaždice pro nástroj pro řešení potíží s nedávnými změnami pro virtuální počítač](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Předplatné Azure Lighthouse se nepodporuje.

- **Dotaz na poskytovatele prostředků Microsoft. ChangeAnalysis se nezdařil** *, protože předplatné služby Azure Lighthouse není podporováno, změny jsou k dispozici pouze v domovském tenantovi předplatného*. Poskytovatel prostředků pro změnu analýz pro uživatele, kteří nejsou v domovském tenantovi, má teď omezení. Pracujeme na řešení tohoto omezení. Pokud se jedná o blokující problém, existuje alternativní řešení, které zahrnuje vytvoření instančního objektu a explicitní přiřazení role pro povolení přístupu.  Obraťte changeanalysishelp@microsoft.com se na kontakt, kde najdete další informace.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Při načítání změn došlo k chybě. Aktualizujte prosím tuto stránku nebo se vraťte později, aby se zobrazily změny.

Jedná se o obecnou chybovou zprávu prezentovanou službou analýza změn aplikace, když se změny nepovedlo načíst. Několik známých příčin:

- Chyba připojení k Internetu z klientského zařízení
- Změna služby Analysis Service, která je dočasně nedostupná aktualizace stránky po několika minutách obvykle tento problém vyřeší. Pokud chyba přetrvává, obraťte se na changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Nemáte dostatečná oprávnění k zobrazení některých změn. Obraťte se na správce předplatného Azure.

Toto je obecná chybová zpráva o neautorizovaném přístupu, která vysvětluje, že aktuální uživatel nemá dostatečná oprávnění k zobrazení změny. Aby bylo možné zobrazit změny infrastruktury vracené službou Azure Resource Graph a Azure Resource Manager, je pro prostředek vyžadován alespoň přístup čtenář. Pro změny souborů v hostu služby Web App a změny konfigurace je potřeba aspoň roli Přispěvatel.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Nepovedlo se zaregistrovat poskytovatele prostředků Microsoft. ChangeAnalysis.

Tato zpráva znamená, že se něco nezdařilo hned po odeslání žádosti o registraci poskytovatele prostředků do uživatelského rozhraní a není v souvislosti s problémem s oprávněním. Pravděpodobně se může jednat o dočasný problém s připojením k Internetu. Zkuste aktualizovat stránku a zkontrolovat připojení k Internetu. Pokud chyba přetrvává, obraťte se na changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Nemáte dostatečná oprávnění k registraci poskytovatele prostředků Microsoft. ChangeAnalysis. Obraťte se na správce předplatného Azure.

Tato chybová zpráva znamená, že vaše role v aktuálním předplatném nemá přiřazený obor **Microsoft. support/registr/Action** . K tomu může dojít v případě, že nejste vlastníkem předplatného a máte oprávnění ke sdíleným přístupům prostřednictvím spolupracovníka (tj. zobrazení přístupu ke skupině prostředků). Chcete-li tento problém vyřešit, obraťte se na vlastníka předplatného, abyste mohli zaregistrovat poskytovatele prostředků **Microsoft. ChangeAnalysis** . To se dá udělat v Azure Portal prostřednictvím **předplatných | Poskytovatelé prostředků** a vyhledejte ```Microsoft.ChangeAnalysis``` a zaregistrujte se v uživatelském rozhraní nebo prostřednictvím Azure PowerShell nebo rozhraní příkazového řádku Azure.

Registrovat poskytovatele prostředků prostřednictvím PowerShellu:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure Resource graphu](../../governance/resource-graph/overview.md), který pomáhá analyzovat změny napájení.