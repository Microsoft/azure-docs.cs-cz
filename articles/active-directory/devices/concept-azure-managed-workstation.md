---
title: Principy zabezpečených pracovních stanic spravovaných azurem – Azure Active Directory
description: Seznamte se s zabezpečenými pracovními stanicemi spravovanými Azure a zjistěte, proč jsou důležité.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672664"
---
# <a name="understand-secure-azure-managed-workstations"></a>Principy zabezpečených pracovních stanic spravovaných azure

Zabezpečené izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a vedoucí pozicí chamitcích kritických služeb. Pokud dojde k ohrožení zabezpečení klientské pracovní stanice, mnoho ovládacích prvků zabezpečení a záruky může selhat nebo být neúčinné.

Tento dokument vysvětluje, co potřebujete pro vytvoření zabezpečené pracovní stanice, často známé jako pracovní stanice s privilegovaným přístupem (PAW). Článek také obsahuje podrobné pokyny k nastavení počátečních ovládacích prvků zabezpečení. Tento návod popisuje, jak cloudové technologie může spravovat službu. Spoléhá se na funkce zabezpečení, které byly zavedeny v systému Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory a Microsoft Intune.

> [!NOTE]
> Tento článek vysvětluje koncept zabezpečené pracovní stanice a její důležitost. Pokud jste již obeznámeni s konceptem a chcete přeskočit na nasazení, navštivte [nasazení zabezpečené pracovní stanice](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Proč je bezpečný přístup k pracovní stanici důležitý

Rychlé přijetí cloudových služeb a schopnost pracovat odkudkoli vytvořila novou metodu využití. Využitím slabých ovládacích prvků zabezpečení na zařízeních, kde správci pracují, mohou útočníci získat přístup k privilegovaným prostředkům.

Privilegované zneužití a útoky dodavatelského řetězce patří mezi pět nejlepších metod, které útočníci používají k narušení organizací. Jsou také druhou nejčastěji zjištěnou taktikou v incidentech hlášených v roce 2018 podle [zprávy Verizon Threat](https://enterprise.verizon.com/resources/reports/dbir/)a zprávy security intelligence [Report](https://aka.ms/sir).

Většina útočníků se řídí následujícími kroky:

1. Průzkum najít cestu dovnitř, často specifické pro průmysl.
1. Analýza shromažďovat informace a identifikovat nejlepší způsob, jak proniknout do pracovní stanice, která je vnímána jako nízká hodnota.
1. Trvalost hledat prostředky k pohybu [příčně](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltrace důvěrných a citlivých údajů.

Během průzkumu útočníci často infiltrují zařízení, která se zdají být podhodnocená nebo podhodnocená. Používají tato zranitelná zařízení k nalezení příležitosti pro boční pohyb a k nalezení administrativních uživatelů a zařízení. Poté, co získají přístup k privilegovaným uživatelským rolím, útočníci identifikují data s vysokou hodnotou a úspěšně tato data exfiltrují.

![Typický kompromisní vzor](./media/concept-azure-managed-workstation/typical-timeline.png)

Tento dokument popisuje řešení, které může pomoci chránit vaše výpočetní zařízení před takovými bočními útoky. Řešení izoluje správu a služby od méně cenných zařízení pro zvýšení produktivity a přeruší řetězec před infiltrací zařízení, které má přístup k citlivým cloudovým prostředkům. Řešení využívá nativní služby Azure, které jsou součástí zásobníku Microsoft 365 Enterprise:

* Intune pro správu zařízení a bezpečný seznam aplikací a adres URL
* Autopilot pro nastavení, nasazení a aktualizaci zařízení
* Azure AD pro správu uživatelů, podmíněný přístup a vícefaktorové ověřování
* Windows 10 (aktuální verze) pro ověření stavu zařízení a uživatelské prostředí
* Ochrana ATP v programu Defender pro ochranu koncových bodů spravanou cloudem, detekci a odezvu
* Azure AD PIM pro správu autorizace a oprávnění za čas (JIT) privilegovaný přístup k prostředkům
* Log Analytics a Sentinel pro monitorování a upozorňování

## <a name="who-benefits-from-a-secure-workstation"></a>Kdo má prospěch ze zabezpečené pracovní stanice?

Všichni uživatelé a operátoři mají prospěch z používání zabezpečené pracovní stanice. Útočník, který ohrožuje počítač nebo zařízení, může zosobnit všechny účty uložené v mezipaměti. Když jsou přihlášeni k zařízení, mohou také používat přihlašovací údaje a tokeny. Toto riziko je důležité zabezpečit zařízení, která se používají pro privilegované role, včetně práv správce. Zařízení s privilegovanými účty jsou cíle pro laterální pohyb a útoky na eskalaci oprávnění. Tyto účty lze použít pro různé prostředky, jako jsou:

* Správce místních nebo cloudových systémů
* Vývojářská pracovní stanice pro kritické systémy
* Správce účtu sociálních médií s vysokou expozicí
* Vysoce citlivá pracovní stanice, jako je platební terminál SWIFT
* Pracovní stanice, která zpracovává obchodní tajemství

Chcete-li snížit riziko, měli byste implementovat zvýšené bezpečnostní kontroly pro privilegované pracovní stanice, které využívají tyto účty. Další informace najdete v [průvodci nasazením funkcí služby Azure Active Directory](../fundamentals/active-directory-deployment-checklist-p2.md), [plán office 365](https://aka.ms/o365secroadmap)a [plán zabezpečení privilegovaného přístupu).](https://aka.ms/sparoadmap)

## <a name="why-use-dedicated-workstations"></a>Proč používat vyhrazené pracovní stanice?

I když je možné přidat zabezpečení stávajícího zařízení, je lepší začít se zabezpečeným základem. Chcete-li, aby vaše organizace byla v nejlepší pozici pro udržení vysoké úrovně zabezpečení, začněte se zařízením, o kterém víte, že je zabezpečené, a implementujte sadu známých ovládacích prvků zabezpečení.

Rostoucí počet útočných vektorů prostřednictvím e-mailu a prohlížení webu je stále těžší být jisti, že zařízení může být důvěryhodný. Tato příručka předpokládá, že vyhrazená pracovní stanice je izolována od standardní produktivity, procházení a e-mailu. Odstranění produktivity, procházení webu a e-mailů ze zařízení může mít negativní dopad na produktivitu. Toto zabezpečení je však obvykle přijatelné pro scénáře, kde úlohy úlohy explicitně nevyžadují a riziko incidentu zabezpečení je vysoká.

> [!NOTE]
> Prohlížení webových stránek zde odkazuje na obecný přístup k libovolným webovým stránkám, které mohou být vysoce rizikovou aktivitou. Takové procházení se výrazně liší od použití webového prohlížeče pro přístup k malému počtu známých webů pro správu pro služby, jako je Azure, Office 365, další poskytovatelé cloudu a aplikace SaaS.

Strategie omezení zpřísní zabezpečení zvýšením počtu a typu ovládacích prvků, které odrazují útočníka od získání přístupu k citlivým prostředkům. Model popsaný v tomto článku používá vrstvené oprávnění návrh a omezuje oprávnění správce na konkrétní zařízení.

## <a name="supply-chain-management"></a>Řízení dodavatelského řetězce

Pro zabezpečenou pracovní stanici je nezbytné řešení dodavatelského řetězce, kde používáte důvěryhodnou pracovní stanici nazvanou "kořen důvěry". Technologie, které je třeba vzít v úvahu při výběru kořene důvěryhodného hardwaru, by měly zahrnovat následující technologie zahrnuté v moderních přenosných počítačích: 

* [Modul důvěryhodné platformy (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker Drive Encryption](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Bezpečné spuštění uefi](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Ovladače a firmware distribuované prostřednictvím služby Windows Update](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualizace a funkce HVCI povolena](/windows-hardware/design/device-experiences/oem-vbs)
* [Ovladače a aplikace HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Ochrana vi/o DMA](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Ochrana systému](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Moderní pohotovostní režim](/windows-hardware/design/device-experiences/modern-standby)

Pro toto řešení bude kořen důvěryhodnosti nasazen pomocí technologie [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) s hardwarem, který splňuje moderní technické požadavky. Chcete-li zabezpečit pracovní stanici, autopilot vám umožní využít zařízení s Windows 10 optimalizovanou pro Microsoft OEM. Tato zařízení jsou dodávána ve známém dobrém stavu od výrobce. Namísto reimaging potenciálně nezabezpečené zařízení, Autopilot můžete transformovat zařízení se systémem Windows do stavu "business-ready". Aplikuje nastavení a zásady, instaluje aplikace a dokonce mění edici Windows 10. Autopilot může například změnit instalaci systému Windows pro zařízení z Windows 10 Pro na Windows 10 Enterprise, aby mohl používat pokročilé funkce.

![Zabezpečené úrovně pracovní ch odsek-stanice](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role a profily zařízení

Tyto pokyny odkazují na několik profilů zabezpečení a rolí, které vám mohou pomoci vytvořit bezpečnější řešení pro uživatele, vývojáře a pracovníky IT. Tyto profily vyvažují použitelnost a rizika pro běžné uživatele, kteří mohou využívat vylepšenou nebo zabezpečenou pracovní stanici. Zde uvedené konfigurace nastavení jsou založeny na oborových standardech. Tento návod ukazuje, jak posílit Windows 10 a snížit rizika spojená s ohrožením zařízení nebo uživatele. Chcete-li využít moderní hardwarové technologie a kořen důvěryhodného zařízení, použijeme [atestaci stavu zařízení](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), která je povolena od **profilu Vysoké zabezpečení.** Tato funkce je k dispozici k zajištění útočníci nemůže být trvalé během předčasného spuštění zařízení. Činí tak pomocí zásad a technologií, které pomáhají spravovat funkce zabezpečení a rizika.
![Zabezpečené úrovně pracovní ch odsek-stanice](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Základní zabezpečení** – Spravovaná standardní pracovní stanice poskytuje dobrý výchozí bod pro většinu domácích a malých podniků. Tato zařízení jsou registrovaná ve službě Azure AD a spravovaná pomocí Intune. Tento profil umožňuje uživatelům spouštět libovolné aplikace a procházet libovolné webové stránky. Mělo by být povoleno řešení ochrany proti malwaru, jako je [Microsoft Defender.](https://www.microsoft.com/windows/comprehensive-security)

* **Rozšířené zabezpečení** – Toto základní chráněné řešení je vhodné pro domácí uživatele, uživatele z řad malých podniků a obecné vývojáře.

   Vylepšená pracovní stanice je způsob založený na zásadách, který zvyšuje zabezpečení nízkého profilu zabezpečení. Poskytuje bezpečné prostředky pro práci s daty zákazníků a zároveň pomocí nástrojů pro zvýšení produktivity, jako je e-mail a prohlížení webových stránek. Pomocí zásad auditu a Intune můžete sledovat vylepšenou pracovní stanici pro chování uživatelů a využití profilu. Nasadíte rozšířený profil pracovní stanice pomocí skriptu Windows10 (1809) a využívá výhod pokročilé ochrany proti malwaru pomocí [rozšířené ochrany před internetovými zprávami (ATP).](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

* **Vysoká bezpečnost** - Nejúčinnějším prostředkem ke snížení útočné plochy pracovní stanice je odstranit schopnost samospravovat pracovní stanici. Odebrání místních práv správce je krok, který zlepšuje zabezpečení, ale může mít vliv na produktivitu, pokud je implementována nesprávně. Vysoký profil zabezpečení vychází z vylepšeného profilu zabezpečení s jednou značnou změnou: odebráním místního správce. Tento profil je určen pro vysoce profilované uživatele: vedoucí pracovníky, mzdové a citlivé uživatele dat, schvalovatele pro služby a procesy.

   Uživatel s vysokým zabezpečením vyžaduje více kontrolované prostředí a zároveň je schopen dělat činnosti, jako je e-mail a procházení webu v jednoduchém použití. Uživatelé očekávají, že funkce, jako jsou soubory cookie, oblíbené položky a další zkratky, budou fungovat. Tito uživatelé však nemusí vyžadovat možnost upravit nebo ladit své zařízení. Také nepotřebují instalovat ovladače. Vysoký profil zabezpečení se nasazuje pomocí skriptu Vysoké zabezpečení – Windows10 (1809).

* **Specialized** – Útočníci cílí na vývojáře a správce IT, protože mohou změnit systémy, které útočníky zajímají. Specializovaná pracovní stanice rozšiřuje zásady pracovní stanice s vysokým zabezpečením tím, že spravuje místní aplikace a omezuje webové stránky. Omezuje také vysoce rizikové možnosti produktivity, jako je ActiveX, Java, pluginy prohlížeče a další ovládací prvky systému Windows. Tento profil nasadíte pomocí skriptu DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline.

* **Zabezpečené** – Útočník, který zkompromituje účet správce, může způsobit značné obchodní škody krádeží dat, změnou dat nebo přerušením služby. V tomto zpevněnéstavu pracovní stanice umožňuje všechny ovládací prvky zabezpečení a zásady, které omezují přímou kontrolu místní správy aplikací. Zabezpečená pracovní stanice nemá žádné nástroje pro zvýšení produktivity, takže je obtížnější zařízení ohrozit. Blokuje nejběžnější vektor pro phishingové útoky: e-mail a sociální média. Zabezpečenou pracovní stanici lze nasadit pomocí skriptu Secure Workstation - Windows10 (1809) SecurityBaseline.

   ![Zabezpečená pracovní stanice](./media/concept-azure-managed-workstation/secure-workstation.png)

   Zabezpečená pracovní stanice poskytuje správci posílenou pracovní stanici, která má jasné řízení aplikací a ochranu aplikací. Pracovní stanice používá ochranu přihlašovacích údajů, ochranu zařízení a ochranu zneužití k ochraně hostitele před škodlivým chováním. Všechny místní disky jsou také šifrovány nástrojem BitLocker.

* **Izolované** – Tento vlastní, offline scénář představuje extrémní konec spektra. Pro tento případ nejsou k dispozici žádné instalační skripty. Možná budete muset spravovat důležitou obchodní funkci, která vyžaduje nepodporovaný nebo neopravený starší operační systém. Například výrobní linka s vysokou hodnotou nebo systém podpory života. Vzhledem k tomu, že zabezpečení je důležité a cloudové služby nejsou k dispozici, můžete tyto počítače spravovat a aktualizovat ručně nebo pomocí izolované architektury doménové struktury služby Active Directory, například prostředí ESAE (Enhanced Security Admin Environment). Za těchto okolností zvažte odebrání veškerého přístupu kromě základních kontrol stavu Intune a ATP.

   * [Požadavek na síťovou komunikaci Intune](/intune/network-bandwidth-use)
   * [Požadavek na komunikaci v síti ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Další kroky

[Nasaďte zabezpečenou pracovní stanici spravovanou Azure](howto-azure-managed-workstation.md).
