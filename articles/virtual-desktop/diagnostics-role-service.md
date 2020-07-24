---
title: Problémy s diagnostikou virtuálních počítačů s Windows – Azure
description: Jak používat funkci diagnostiky virtuálních klientských počítačů Windows k diagnostice problémů.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b9e63ca3c6822f4fd9ff215ccddaaf82ff55369
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075653"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Identifikace a Diagnostika problémů s virtuálními počítači s Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Virtuální plocha Windows nabízí diagnostické funkce, které správci umožňují identifikovat problémy přes jedno rozhraní. Další informace o diagnostických funkcích virtuálního klienta Windows najdete v tématu [použití Log Analytics pro diagnostické funkce](diagnostics-log-analytics.md).

Připojení, která nedosáhnou virtuálního klienta Windows, se nezobrazí ve výsledcích diagnostiky, protože samotná služba role diagnostiky je součástí virtuálního klienta Windows. Problémy s připojením k virtuálnímu počítači s Windows se můžou vyskytnout, když koncový uživatel dochází k problémům se síťovým připojením.

## <a name="common-error-scenarios"></a>Běžné chybové scénáře

Scénáře chyb jsou rozdělené do kategorií interní pro službu a externí pro virtuální počítače s Windows.

* Vnitřní problém: Určuje scénáře, které zákazník nemůže zmírnit a který je potřeba vyřešit jako problém podpory. Při poskytování zpětné vazby prostřednictvím [odborné komunity pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)zadejte ID korelace a přibližný časový rámec, kdy k problému došlo.
* Externí problém: vztah ke scénářům, které může zákazník zmírnit. Jedná se o externí funkce pro virtuální počítače s Windows.

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

### <a name="external-connection-error-codes"></a>Kódy chyb externího připojení

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

## <a name="next-steps"></a>Další kroky

Další informace o rolích v rámci virtuálních počítačů s Windows najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).

Seznam dostupných rutin PowerShellu pro virtuální počítač s Windows najdete v [referenčních informacích k prostředí PowerShell](/powershell/windows-virtual-desktop/overview).
