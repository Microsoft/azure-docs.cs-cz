---
title: Firemní pracovní účty a Partnerské centrum
description: Jak zkontrolovat, zda má vaše společnost pracovní účet nastavený u Microsoftu, vytvořit nový pracovní účet nebo nastavit více pracovních účtů pro použití s Partnerským centrem.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 3c0ee2f8d5291f7904435dea32d913adeaaf25c5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262362"
---
# <a name="company-work-accounts-and-partner-center"></a>Firemní pracovní účty a Partnerské centrum

Partnerské centrum používá pracovní účty společnosti, známé také jako klienty Služby Azure Active Directory (AD), ke správě přístupu k účtu pro více uživatelů, oprávnění k řízení, skupin hostitelům a aplikacím a k udržování dat profilu. Propojením domény pracovního e-mailového účtu vaší společnosti s účtem Partnerského centra se zaměstnanci vaší společnosti mohou přihlásit do Centra partnerů a spravovat nabídky marketplace pomocí vlastních uživatelských jmen a hesel pracovního účtu.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Zkontrolujte, zda vaše společnost již má pracovní účet

Pokud se vaše společnost přihlásila k odběru cloudové služby Microsoftu, jako je Azure, Microsoft Intune nebo Office 365, pak už máte doménu pracovního e-mailového účtu (označovanou také jako tenant adslužeb Azure Directory), kterou lze použít s Partnerským centrem.

Chcete-li zkontrolovat následující kroky, postupujte takto:
1. Přihlaste se k https://portal.azure.comportálu pro správu Azure na adrese .
2. V nabídce levé navigace vyberte **Azure Active Directory** a pak vyberte Vlastní **názvy domén**.
3. Pokud již máte pracovní účet, bude uveden název vaší domény.

Pokud vaše společnost ještě nemá pracovní účet, bude vytvořen během procesu registrace v Centru partnerů.

## <a name="set-up-multiple-work-accounts"></a>Nastavení více pracovních účtů

Než se rozhodnete použít existující pracovní účet, zvažte, kolik uživatelů v pracovním účtu bude muset získat přístup k Partnerskému centru. Pokud máte v pracovním účtu uživatele, kteří nebudou potřebovat přístup k Partnerskému centru, můžete zvážit vytvoření více pracovních účtů, aby byli v konkrétním účtu zastoupeni pouze uživatelé, kteří budou potřebovat přístup k Partnerskému centru.

## <a name="create-a-new-work-account"></a>Vytvoření nového pracovního účtu

Chcete-li vytvořit nový pracovní účet pro vaši společnost, postupujte podle následujících kroků. Možná budete muset požádat o pomoc toho, kdo má oprávnění správce pro účet Microsoft Azure vaší společnosti.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).
2. V levé navigační nabídce vyberte**položku Uživatelé** **služby Azure Active Directory** -> .
3. Vyberte **Nový uživatel** a vytvořte nový pracovní účet Azure zadáním jména a e-mailové adresy. Ujistěte se, že **role adresáře** je **nastavena** na uživatele a zaškrtněte **políčko Zobrazit heslo** v dolní části, chcete-li zobrazit a poznamenat si automaticky vygenerované heslo.
4. Vyberte **Vytvořit,** chcete-li uložit nového uživatele.

E-mailová adresa uživatelského účtu musí být ověřeným názvem domény ve vašem adresáři. Všechny ověřené domény ve vašem adresáři můžete uvést tak, že v levé navigační nabídce vyberete**vlastní názvy domén** **služby Azure Active Directory.** -> 

Další informace o přidávání vlastních domén ve službě Azure Active Directory najdete v [tématu Přidání nebo přidružení domény ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Poradce při potížích s přihlášením pracovního e-mailu

Pokud máte potíže s přihlášením k vašemu pracovnímu účtu (označované také jako váš klient Azure AD), najděte scénář v následujícím diagramu, který nejlépe odpovídá vaší situaci a postupujte podle doporučených kroků.

![Diagram pro řešení potíží s přihlášením pracovního účtu](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Další kroky

- [Správa účtu komerčního tržiště v Partnerském centru](./manage-account.md) 
