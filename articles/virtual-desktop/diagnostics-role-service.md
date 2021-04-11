---
title: Problémy s diagnostikou virtuálních počítačů s Windows – Azure
description: Jak používat funkci diagnostiky virtuálních klientských počítačů Windows k diagnostice problémů.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cf654f07cbbd729fac8feb9e2f8f7ceff471ae6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446991"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Identifikace a Diagnostika problémů s virtuálními počítači s Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Virtuální plocha Windows nabízí diagnostické funkce, které správci umožňují identifikovat problémy přes jedno rozhraní. Další informace o diagnostických funkcích virtuálního klienta Windows najdete v tématu [použití Log Analytics pro diagnostické funkce](diagnostics-log-analytics.md).

Připojení, která nedosáhnou virtuálního klienta Windows, se nezobrazí ve výsledcích diagnostiky, protože samotná služba role diagnostiky je součástí virtuálního klienta Windows. Problémy s připojením k virtuálnímu počítači s Windows se můžou vyskytnout, když koncový uživatel dochází k problémům se síťovým připojením.

## <a name="common-error-scenarios"></a>Běžné chybové scénáře

Tabulka WVDErrors sleduje chyby napříč všemi typy aktivit. Sloupec s názvem "ServiceError" poskytuje další příznak označený buď "true" nebo "false". Tento příznak vám sdělí, jestli chyba souvisí se službou.

* Pokud je hodnota "true", tým služby již mohl tento problém prozkoumat. Pokud se to týká prostředí pro uživatele a zobrazí se vám vysoký počet pokusů, doporučujeme odeslat lístek podpory pro virtuální počítač s Windows.
* Pokud je hodnota false, může to být chybná konfigurace, kterou můžete opravit sami. Chybová zpráva vám může sdělit, kde začít.

V následující tabulce jsou uvedeny běžné chyby, ke kterým můžou správci běžet.

>[!NOTE]
>Tento seznam obsahuje nejběžnější chyby a je aktualizovaný na pravidelných tempo. Abyste měli jistotu, že máte nejaktuálnější informace, nezapomeňte se vrátit k tomuto článku aspoň jednou za měsíc.

## <a name="management-errors"></a>Chyby správy

|Chybová zpráva|Navrhované řešení|
|---|---|
|Nepovedlo se vytvořit registrační klíč. |Registrační token se nedal vytvořit. Zkuste ho vytvořit znovu s kratším časem vypršení platnosti (1 hodina a 1 měsíc). |
|Nepovedlo se odstranit registrační klíč|Registrační token se nepovedlo odstranit. Zkuste ho znovu odstranit. Pokud to pořád nefunguje, ověřte pomocí PowerShellu, jestli je token pořád přítomen. Pokud tam je, odstraňte ho pomocí PowerShellu.|
|Nepovedlo se změnit režim vyprázdnění hostitele relace. |Na virtuálním počítači se nepovedlo změnit režim vyprázdnění. Ověřte stav virtuálního počítače. Pokud není dostupný virtuální počítač, režim vyprázdnění se nedá změnit.|
|Nepovedlo se odpojit uživatelské relace. |Nepovedlo se odpojit uživatele od virtuálního počítače. Ověřte stav virtuálního počítače. Pokud virtuální počítač není k dispozici, relaci uživatele nelze odpojit. Pokud je virtuální počítač dostupný, zkontrolujte stav uživatelské relace a zjistěte, jestli je odpojený. |
|Nepovedlo se odhlásit všechny uživatele v rámci hostitele relace. |Nepovedlo se podepsat uživatele z virtuálního počítače. Ověřte stav virtuálního počítače. Pokud není k dispozici, uživatelé se nemůžou odhlásit. Zkontrolujte stav uživatelské relace a zjistěte, zda jsou již odhlášeny. Můžete vynutit odhlášení pomocí prostředí PowerShell. |
|Nepovedlo se zrušit přiřazení uživatele ze skupiny aplikací.|Nepovedlo se zrušit publikování skupiny aplikací pro uživatele. Zkontrolujte, jestli je uživatel k dispozici ve službě Azure AD. Zkontrolujte, jestli je uživatel součástí skupiny uživatelů, na kterou je publikovaná skupina aplikací. |
|Při načítání dostupných umístění došlo k chybě. |Ověřte umístění virtuálního počítače používaného v Průvodci vytvořením fondu hostitelů. Pokud není v tomto umístění k dispozici obrázek, přidejte do tohoto umístění obrázek nebo vyberte jiné umístění virtuálního počítače. |

### <a name="connection-error-codes"></a>Kódy chyb připojení

|Číselný kód|Kód chyby|Navrhované řešení|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Hostitel relace není správně připojen ke službě Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Připojení se nezdařilo, protože hostitel relace není k dispozici. Ověřte stav hostitele relace.|
|-2146233088|ConnectionFailedClientDisconnect|Pokud se tato chyba zobrazí často, ujistěte se, že je počítač uživatele připojený k síti.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Relace, ke které se hostitelský uživatel pokusil připojit, není v pořádku. Ladit virtuální počítač.|
|-2146233088|ConnectionFailedUserNotAuthorized|Uživatel nemá oprávnění pro přístup k publikované aplikaci nebo k ploše. Tato chyba se může zobrazit po odebrání publikovaných prostředků správcem. Požádejte uživatele, aby tento informační kanál aktualizoval v aplikaci Vzdálená plocha.|
|2|FileNotFound|Aplikace, ke které se uživatel pokusil získat přístup, je buď nesprávně nainstalovaná, nebo nastavená na nesprávnou cestu.<br><br>Při publikování nových aplikací, když má uživatel aktivní relaci, uživatel nebude mít k této aplikaci přístup. Aby uživatel mohl získat přístup k aplikaci, je nutné ukončit a znovu spustit relaci. |
|3|InvalidCredentials|Uživatelské jméno nebo heslo, které uživatel zadal, neodpovídá žádnému z existujících uživatelských jmen a hesel. Zkontrolujte přihlašovací údaje pro překlepy a zkuste to znovu.|
|8|ConnectionBroken|Spojení mezi klientem a bránou nebo serverem bylo vyřazeno. Není nutná žádná akce, pokud dojde k neočekávanému chování.|
|14|UnexpectedNetworkDisconnect|Připojení k síti bylo vyřazeno. Požádejte uživatele, aby se připojil znovu.|
|24|ReverseConnectFailed|Hostitelský virtuální počítač nemá žádný přímý dohled, který by Brána VP. Zajistěte, aby se IP adresa brány mohla přeložit.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Chyba: přiřazení uživatelů nejde přidat do skupiny aplikací.

Po přiřazení uživatele ke skupině aplikací Azure Portal zobrazí upozornění, které říká "ukončení relace" nebo "problémy s ověřováním – Microsoft_Azure_WVD rozšíření". Stránka přiřazení potom nenačte a pak stránky zastaví načítání po celém Azure Portal (například Azure Monitor, Log Analytics, Service Health a tak dále).

**Příčina:** Došlo k potížím se zásadami podmíněného přístupu. Azure Portal se snaží získat token pro Microsoft Graph, který je závislý na SharePointu Online. Zákazník má zásadu podmíněného přístupu nazvanou "systém Microsoft Office 365 podmínky použití datového úložiště", která vyžaduje, aby uživatelé přijali podmínky použití pro přístup k úložišti dat. Ale ještě nejsou přihlášené, takže Azure Portal nemůže získat token.

**Oprava:** Před přihlášením k Azure Portal musí správce nejdřív přihlašovat se ke službě SharePoint a přijmout podmínky použití. Pak by se měly být schopni přihlásit k Azure Portal, jako je normální.

## <a name="next-steps"></a>Další kroky

Další informace o rolích v rámci virtuálních počítačů s Windows najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).

Seznam dostupných rutin PowerShellu pro virtuální počítač s Windows najdete v [referenčních informacích k prostředí PowerShell](/powershell/windows-virtual-desktop/overview).
