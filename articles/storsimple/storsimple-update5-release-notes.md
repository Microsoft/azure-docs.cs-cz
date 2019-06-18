---
title: Poznámky k verzi StorSimple 8000 Series Update 5 | Dokumentace Microsoftu
description: Popisuje nové funkce, problémy a řešení pro StorSimple 8000 Series Update 5.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 462cbd6261723aa91bbfd23292611e758a800ed2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844087"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Poznámky k verzi StorSimple 8000 Series Update 5

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikovat kritické otevřené problémy pro StorSimple 8000 Series Update 5. Obsahují také seznam aktualizací softwaru StorSimple jsou v ní obsažené.

Update 5 můžete použít na jakékoli zařízení StorSimple s verzí Update 0.1 prostřednictvím aktualizace Update 4. Verze zařízení spojené s aktualizací Update 5 je 6.3.9600.17845.

Projděte si informace obsažené v poznámkách k verzi, než nasadíte aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a musí se nainstalovat hned. Další informace najdete v tématu Jak [použít aktualizaci 5](storsimple-8000-install-update-5.md).
> * Aktualizace 5 má zařízení software, firmwaru disku, zabezpečení operačního systému a dalších aktualizací operačního systému. Trvá přibližně 4 hodin k instalaci této aktualizace. Aktualizace firmwaru disku je rušivé aktualizace a má za následek výpadek pro vaše zařízení. Doporučujeme nainstalovat Update 5 a udržuje vaše zařízení aktuální.
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože děláme postupné zavádění aktualizací. Počkejte pár dní a pak vyhledávání aktualizací znovu jako tyto aktualizace bude brzy k dispozici.

## <a name="whats-new-in-update-5"></a>Co je nového ve verzi Update 5

Byly provedeny následující klíčová vylepšení a opravy chyb ve verzi Update 5.

* **Použití nástroje Azure Active Directory (AAD) k ověření pomocí služby Správce zařízení StorSimple** – od aktualizace 5 výš, Azure Active Directory se používá k ověření ve službě Správce zařízení StorSimple. Staré mechanismus ověřování se přestanou používat. prosince 2017. Všichni uživatelé musí obsahovat nové ověřovací adresy URL v jeho pravidlech brány firewall. Další informace najdete v části [ověřování adresy URL podle požadavků na síť pro zařízení StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Pokud ověřovací adresa URL není zahrnut v pravidlech brány firewall, uživatelé uvidí kritickou výstrahu, která se jejich zařízení StorSimple nemohlo ověřit ve službě. Pokud se uživatelům zobrazuje toto upozornění, musí obsahovat nové adresy URL ověřování. Další informace najdete v části [StorSimple sítě výstrahy](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nová verze StorSimple Snapshot Manageru** – nová verze StorSimple Snapshot Manageru je vydána s aktualizací Update 5 a je kompatibilní s všechna zařízení StorSimple, která jsou spuštěna verze Update 4 nebo novější. Doporučujeme aktualizovat na tuto verzi. Předchozí verze StorSimple Snapshot Manageru se používá pro zařízení StorSimple s aktualizací Update 3 nebo dřívější. [Stáhněte si příslušnou verzi StorSimple Snapshot Manageru](https://www.microsoft.com/en-us/download/details.aspx?id=44220) věnované [nasazení StorSimple Snapshot Manageru](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Chyby opravené v Update 5

Následující tabulka obsahuje souhrn problémy opravené ve verzi Update 5.

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Vzdálenou komunikaci prostředí Windows PowerShell |V předchozí verzi by uživatel obdrží chybu při pokusu o navázání vzdáleného připojení k řešení StorSimple Cloud Appliance přes Windows PowerShell. Tento problém byl způsobila kořenové a opravené v této verzi. |Ne |Ano |
| 2 |Šablony šířky pásma |V dřívější verzi došlo k nějakému problému s šablony šířky pásma, z kterých vzniklo menší šířku pásma, než co bylo toto zařízení nakonfigurované pro. Tento problém je vyřešen v této verzi. |Ano |Ano |
| 3 |Převzetí služeb při selhání |V předchozí verzi Pokud zařízení s velkým množstvím svazků se převzetí služeb při selhání na jiné zařízení spuštěna verze Update 4, proces selže při pokusu o použití záznamy řízení přístupu. Tento problém je vyřešen v této verzi. |Ano |Ano |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Známé problémy ve verzi Update 5 z předchozích verzí

Nejsou žádné nové známé problémy ve verzi Update 5. Seznam problémů přenesou na 5 aktualizace z předchozích verzí, přejděte na [zpráva k vydání verze Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Připojená k sériovým SCSI (SAS) kontroleru a aktualizace firmwaru ve verzi Update 5

Tato verze má řadič SAS a aktualizace LSI ovladače a firmware. Další informace o tom, jak tyto aktualizace nainstalovat, naleznete v tématu [instalace aktualizace 5](storsimple-8000-install-update-5.md) zařízení StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizace řešení StorSimple Cloud Appliance ve verzi Update 5

Tato aktualizace nejde použít u řešení StorSimple Cloud Appliance (označované také jako virtuální zařízení). Nová Cloudová zařízení musí být vytvořené pomocí image Update 5. Informace o tom, jak vytvořit řešení StorSimple Cloud Appliance, přejděte na [nasadit a spravovat řešení StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Další krok

Zjistěte, jak [instalace aktualizace 5](storsimple-8000-install-update-5.md) zařízení StorSimple.

