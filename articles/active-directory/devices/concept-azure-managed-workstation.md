---
title: Pochopení zabezpečení pracovních stanic spravovaných Azure – Azure Active Directory
description: Přečtěte si o zabezpečených pracovních stanicích spravovaných Azure a zjistěte, proč jsou důležité.
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
ms.openlocfilehash: 4298518dae9afdf197321073846fa1ab9262ec0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601380"
---
# <a name="understand-secure-azure-managed-workstations"></a>Pochopení zabezpečení pracovních stanic spravovaných Azure

Zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a provozovatelé důležitých služeb. Pokud dojde k ohrožení zabezpečení pracovní stanice klienta, mnohé bezpečnostní mechanismy a záruky můžou selhat nebo být neúčinné.

Tento dokument vysvětluje, co potřebujete pro vytvoření zabezpečené pracovní stanice, často označované jako pracovní stanice s privilegovaným přístupem (PRIVILEGOVANÝM přístupem). Článek obsahuje také podrobné pokyny k nastavení počátečních ovládacích prvků zabezpečení. V tomto návodu se dozvíte, jak může Cloudová technologie spravovat službu. Spoléhá se na funkce zabezpečení, které byly představené ve Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory a Microsoft Intune.

> [!NOTE]
> Tento článek vysvětluje koncept zabezpečené pracovní stanice a její důležitosti. Pokud už jste obeznámeni s konceptem a chcete přejít k nasazení, navštivte téma [nasazení zabezpečené pracovní stanice](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Proč je důležitá zabezpečený přístup k pracovní stanici

Rychlé přijetí cloudových služeb a schopnost pracovat odkudkoli, vytvořil novou metodu využívání. Díky zneužití slabého bezpečnostního řízení v zařízeních, kde správci pracují, můžou útočníci získat přístup k privilegovaným prostředkům.

Privilegované zneužití a útoky dodavatelských řetězců jsou mezi pěti nejvyššími metodami, které útočníci používají pro porušení organizací. Jsou to taky druhá nejčastěji zjištěná taktiku ve incidentech hlášených v 2018 v závislosti na [sestavě hrozeb Verizon](https://enterprise.verizon.com/resources/reports/dbir/)a [sestavě Security Intelligence](https://aka.ms/sir).

Většina útočníků se řídí těmito kroky:

1. Rekognoskace pro nalezení způsobu, který je často určený pro odvětví.
1. Analýza pro shromažďování informací a určení nejlepšího způsobu, jak vyfiltrovat pracovní stanici, která je vnímaná jako nízká hodnota.
1. Trvalost pro hledání prostředků k [pozdějšímu](https://en.wikipedia.org/wiki/Network_Lateral_Movement)přesunutí.
1. Exfiltrace důvěrných a citlivých dat.

V průběhu rekognoskace útočníci často nefiltrují zařízení, která se zdají být nízké nebo mají hodnotu s nízkou rizikovostí. Používají tato zranitelná zařízení k vyhledání příležitosti pro příčný pohyb a k nalezení administrativních uživatelů a zařízení. Po získání přístupu k privilegovaným rolím uživatelů identifikují útočníci data s vysokou hodnotou a úspěšně exfiltrovat tato data.

![Typický vzor ohrožení zabezpečení](./media/concept-azure-managed-workstation/typical-timeline.png)

Tento dokument popisuje řešení, které vám může přispět k ochraně výpočetních zařízení před těmito bočními útoky. Řešení izoluje správu a služby od méně cenných kancelářských zařízení, přerušuje řetěz před tím, než zařízení, které má přístup k citlivým cloudovým prostředkům, může být nefiltrované. Řešení používá nativní služby Azure, které jsou součástí Microsoft 365 Enterprise stacku:

* Intune pro správu zařízení a bezpečný seznam aplikací a adres URL
* Autopilot pro nastavení zařízení, nasazení a aktualizaci
* Azure AD pro správu uživatelů, podmíněný přístup a Multi-Factor Authentication
* Windows 10 (aktuální verze) pro ověření stavu zařízení a činnost koncového uživatele
* ATP – ATP pro cloudově spravovaná Endpoint Protection, detekce a reakce
* Azure AD PIM pro správu autorizace a privilegovaného přístupu k prostředkům za běhu (just-in-time)
* Log Analytics a Sentinel pro monitorování a upozorňování

## <a name="who-benefits-from-a-secure-workstation"></a>Kdo má z zabezpečené pracovní stanice výhody?

Všichni uživatelé a operátoři využívají zabezpečenou pracovní stanici. Útočník, který ohrozí počítač nebo zařízení, může zosobnit všechny účty v mezipaměti. Když se přihlásíte k zařízení, můžou taky používat přihlašovací údaje a tokeny. Toto riziko je důležité zabezpečit zařízení, která se používají pro privilegované role, včetně oprávnění správce. Zařízení s privilegovanými účty jsou cílem útoků na pohyb a zvýšení úrovně oprávnění. Tyto účty je možné použít pro různé prostředky, jako například:

* Správce místních nebo cloudových systémů
* Pracovní stanice pro vývojáře pro důležité systémy
* Správce účtu sociálních médií s vysokou expozicí
* Vysoce citlivá pracovní stanice, jako je například platba terminálu SWIFT
* Obchodní tajemství pro zpracování pracovních stanic

Pro snížení rizika byste měli implementovat zvýšené řízení zabezpečení pro privilegované pracovní stanice, které využívají tyto účty. Další informace najdete v tématu [Průvodce nasazením funkcí Azure Active Directory](../fundamentals/active-directory-deployment-checklist-p2.md), [Microsoft 365 plán](https://aka.ms/o365secroadmap)a [zabezpečení privilegovaného přístupu](https://aka.ms/sparoadmap).

## <a name="why-use-dedicated-workstations"></a>Proč používat vyhrazené pracovní stanice?

I když je možné přidat zabezpečení do stávajícího zařízení, je lepší začít se zabezpečenou základnou. Pokud chcete svou organizaci umístit do nejlepšího umístění, abyste zachovali vysokou úroveň zabezpečení, začněte u zařízení, které znáte, zabezpečit a implementujte sadu známých kontrolních mechanismů zabezpečení.

Větší počet vektorů útoku prostřednictvím e-mailu a procházení webu vám umožní zajistit, aby zařízení bylo důvěryhodné. V tomto průvodci se předpokládá, že vyhrazená pracovní stanice je izolovaná od standardní produktivity, procházení a e-mailu. Odebrání produktivity, procházení webu a e-maily ze zařízení může mít negativní dopad na produktivitu. Toto zabezpečení je však obvykle přijatelné ve scénářích, kdy úlohy úlohy je výslovně nevyžadují a riziko incidentu zabezpečení je vysoké.

> [!NOTE]
> Procházení webu tady odkazuje na obecný přístup k libovolným webům, které můžou představovat vysoce rizikové aktivity. Toto procházení se liší od použití webového prohlížeče pro přístup k malému počtu dobře známých webů pro správu pro služby, jako je Azure, Microsoft 365, ostatní poskytovatelé cloudu a SaaS aplikace.

Strategie omezení zvyšují zabezpečení tím, že zvyšují počet a typ ovládacích prvků, které útočníkovi brání v získání přístupu k citlivým prostředkům. Model popsaný v tomto článku používá návrh vrstveného oprávnění a omezuje oprávnění správce na konkrétní zařízení.

## <a name="supply-chain-management"></a>Správa dodavatelských řetězců

Základem pro zabezpečenou pracovní stanici je řešení dodavatelského řetězce, ve kterém používáte důvěryhodnou pracovní stanici s názvem kořen vztahu důvěryhodnosti. Technologie, která se musí vzít v úvahu při výběru kořene hardwaru vztahu důvěryhodnosti, by měla zahrnovat následující technologie, které jsou součástí moderních přenosných počítačů: 

* [ČIP TPM (Trusted Platform Module) 2,0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker Drive Encryption](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Zabezpečené spouštění UEFI](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Ovladače a firmware distribuované prostřednictvím web Windows Update](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualizace a HYPERVISOREM HVCI povoleny](/windows-hardware/design/device-experiences/oem-vbs)
* [Ovladače a aplikace HYPERVISOREM HVCI – připravené](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Ochrana vstupu/výstupu DMA](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Ochrana systému](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Moderní pohotovostní režim](/windows-hardware/design/device-experiences/modern-standby)

Pro toto řešení se kořen důvěryhodnosti nasadí pomocí technologie [Microsoft autopilot](/windows/deployment/windows-autopilot/windows-autopilot) s hardwarem, který splňuje moderní technické požadavky. Za účelem zabezpečení pracovní stanice vám nástroj autopilot umožňuje využívat zařízení s Windows 10 optimalizovaná výrobcem OEM. Tato zařízení jsou od výrobce dodávána se známým dobrým stavem. Místo opětovného navýšení obrazu potenciálně nezabezpečeného zařízení může tento autopilot transformovat zařízení s Windows do stavu připraveného pro přípravu do zaměstnání. Aplikuje nastavení a zásady, nainstaluje aplikace a dokonce změní edici Windows 10. Například autopilot může změnit instalaci Windows zařízení z Windows 10 pro na Windows 10 Enterprise, aby mohl používat pokročilé funkce.

![Úrovně zabezpečení pracovních stanic](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role a profily zařízení

Tento návod odkazuje na několik profilů zabezpečení a rolí, které vám pomůžou vytvořit bezpečnější řešení pro uživatele, vývojáře a pracovníky IT. Tyto profily vyrovnávají použitelnost a rizika pro běžné uživatele, kteří můžou využívat rozšířenou nebo zabezpečenou pracovní stanici. Zde uvedené konfigurace nastavení jsou založené na standardech, které byly přijaty v oborech. V těchto pokynech se dozvíte, jak posílit Windows 10 a snížit rizika spojená s ohrožením zařízení nebo uživatele. Pokud chcete využít výhod moderní hardwarové technologie a kořenového adresáře důvěryhodného zařízení, použijeme [ověření stavu zařízení](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), která je povolená od **vysokého profilu zabezpečení** . Tato funkce je k dispozici, aby nemohly být při předčasném spuštění zařízení trvalé. K tomu slouží zásady a technologie, které vám pomůžou se správou bezpečnostních funkcí a rizik.
![Úrovně zabezpečení pracovních stanic](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Základní zabezpečení** – spravovaná standardní pracovní stanice nabízí dobrý výchozí bod pro většinu domácích a malých obchodních použití. Tato zařízení jsou registrovaná v Azure AD a spravovaná pomocí Intune. Tento profil umožňuje uživatelům spouštět libovolné aplikace a procházet libovolný web. Mělo by být povoleno řešení ochrany proti malwaru, jako je [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) .

* **Rozšířené zabezpečení** – toto chráněné řešení je vhodné pro domácí uživatele, malé firmy a obecné vývojáře.

   Rozšířená pracovní stanice je způsob, jak zvýšit zabezpečení nízkého profilu zabezpečení na základě zásad. Poskytuje zabezpečené prostředky pro práci s Zákaznickými daty a zároveň používá nástroje pro zvýšení produktivity, jako jsou e-maily a procházení webu. Pomocí zásad auditu a Intune můžete monitorovat rozšířenou pracovní stanici pro chování uživatelů a používání profilů. Rozšířený profil pracovní stanice nasadíte pomocí skriptu Windows10 (1809) a využijete pokročilou ochranu proti malwaru pomocí [rozšířené ochrany před internetovými útoky (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Vysoké zabezpečení** – nejúčinnější způsob, jak omezit prostor pro útok na pracovní stanici, je odebrat možnost samoobslužně spravovat pracovní stanici. Odebrání oprávnění místního správce je krok, který vylepšuje zabezpečení, ale může mít vliv na produktivitu, pokud je nesprávně implementován. Profil vysokého zabezpečení se vytváří v rozšířeném profilu zabezpečení s jednou významnou změnou: odebrání místního správce. Tento profil je určený pro uživatele s vysokým profilem: vedoucí pracovníky, mzdy a citlivá data uživatelů, schvalovatele pro služby a procesy.

   Uživatel s vysokým zabezpečením vyžaduje více řízených prostředí a stále dokáže provádět aktivity, jako je například e-maily a procházení webu v jednoduchém prostředí pro použití. Uživatelé očekávají funkce, jako jsou soubory cookie, oblíbené položky a další klávesové zkratky, které fungují. Tito uživatelé ale nemusí vyžadovat možnost upravovat nebo ladit zařízení. Nepotřebují také instalovat ovladače. Profil vysokého zabezpečení se nasazuje pomocí skriptu High Security-Windows10 (1809).

* **Specializované** – útočníci zaměřené na vývojáře a správce IT, protože mohou změnit systémy, které mají zájem na útočníky. Specializovaná pracovní stanice se rozbalí v zásadách vysoké pracovní stanice zabezpečení tím, že spravuje místní aplikace a omezuje weby. Omezuje také vysoce rizikové možnosti, jako jsou ActiveX, Java, moduly plug-in prohlížeče a další ovládací prvky Windows. Tento profil nasadíte pomocí skriptu DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline.

* **Zabezpečený** – útočník, který narušuje účet správce, může způsobit významnou škodu v případě krádeže dat, změny dat nebo přerušení služeb. V tomto posíleném stavu pracovní stanice povoluje všechny kontrolní mechanismy a zásady zabezpečení, které omezují přímou kontrolu nad správou místních aplikací. Zabezpečená pracovní stanice nemá žádné nástroje pro zvýšení produktivity, takže se zařízení obtížně ohrozí. Blokuje nejběžnější vektor útoků phishingu: e-mail a sociální média. Zabezpečenou pracovní stanici je možné nasadit pomocí skriptu Secure Workstation-Windows10 (1809) SecurityBaseline.

   ![Zabezpečená pracovní stanice](./media/concept-azure-managed-workstation/secure-workstation.png)

   Zabezpečená pracovní stanice poskytuje správcům posílenou pracovní stanici, která má jasný ovládací prvek aplikace a ochranu Application Guard. Pracovní stanice k ochraně hostitele před škodlivým chováním používá ochranu Credential Guard, ochranu zařízení a zneužití ochrany. Všechny místní disky jsou také šifrovány pomocí nástroje BitLocker.

* **Izolovaný** – tento vlastní, offline scénář představuje extrémní konec spektra. Pro tento případ nejsou k dispozici žádné instalační skripty. Možná budete muset spravovat kritickou obchodní funkci, která vyžaduje nepodporovaný nebo neopravený starší operační systém. Například vysoká hodnota výrobní linky nebo systému podpory životního cyklu. Vzhledem k tomu, že je zabezpečení důležité a cloudové služby nejsou k dispozici, můžete tyto počítače spravovat a aktualizovat ručně nebo s izolovanou doménovou strukturou služby Active Directory, jako je například rozšířené prostředí pro správu zabezpečení (zvýšeným zabezpečením). V takových případech zvažte odebrání veškerého přístupu, s výjimkou základních kontrol služby Intune a ATP.

   * [Požadavek na síťovou komunikaci Intune](/intune/network-bandwidth-use)
   * [Požadavek na síťovou komunikaci ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Další kroky

[Nasaďte zabezpečenou pracovní stanici spravovanou v Azure](howto-azure-managed-workstation.md).
