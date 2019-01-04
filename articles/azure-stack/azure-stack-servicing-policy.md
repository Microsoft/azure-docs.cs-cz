---
title: Údržba zásad služby Azure Stack | Dokumentace Microsoftu
description: Další informace o službě Azure Stack údržby, zásad a tom, jak zajistit integrovaný systém v podporovaném stavu.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: sethm
ms.reviewer: harik
ms.openlocfilehash: 1865b4fa03f774fb4cbe69a5c8b17b096981ebc8
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632785"
---
# <a name="azure-stack-servicing-policy"></a>Údržba zásad služby Azure Stack

Tento článek popisuje údržby zásady pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu. 

## <a name="download-update-packages-for-integrated-systems"></a>Stáhněte si balíčky aktualizací pro integrované systémy

Microsoft vydá úplné měsíční aktualizace balíčků i balíčky oprav hotfix umožní řešit konkrétní problémy. 

Balíčky s měsíčním aktualizace jsou hostované v zabezpečené koncový bod Azure. Můžete je ručně pomocí Stáhnout [nástroj pro stahování aktualizací Azure Stack](http://aka.ms/azurestackupdatedownload). Pokud je připojené jednotky škálování, aktualizace se automaticky zobrazí na portálu správce jako **k dispozici je aktualizace**. Úplné a měsíční aktualizace balíčků jsou dobře zdokumentovaná při každém vydání. Další informace o každé vydané verze, můžete kliknout na libovolném vydání z [aktualizace balíčku vydávání verzí](#update-package-release-cadence) části tohoto článku.

Balíčky aktualizací opravy hotfix jsou hostované ve stejné zabezpečené koncový bod Azure. Můžete je ručně pomocí vložených odkazů v každém z článků znalostní báze KB příslušné opravy hotfix; stáhnout například [Azure Stack Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Podobně jako úplné, jsou měsíční aktualizace balíčků, operátoři Azure stacku můžete stáhnout soubory .xml, soubor .bin a .exe a naimportovat pomocí postupu v [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md). Operátoři Azure stacku s připojených jednotkách uvidí opravy hotfix, automaticky se na portálu správce se zprávou **k dispozici je aktualizace**.

Pokud není připojené jednotky škálování a chcete, abyste dostávali oznámení o každém vydání opravy hotfix, přihlášení k odběru [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss … ) nebo [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom …) informačního kanálu, které jste si poznamenali v jednotlivých verzích.  

## <a name="update-package-types"></a>Typy aktualizace balíčků

Existují dva typy balíčků aktualizací pro integrované systémy: 

- **Aktualizace softwaru Microsoft**. Společnost Microsoft zodpovídá za začátku do konce životní cyklus údržby pro balíčky aktualizací softwaru společnosti Microsoft. Tyto balíčky můžou zahrnovat nejnovější aktualizace zabezpečení Windows serveru, které se netýkají zabezpečení aktualizace a aktualizace funkcí služby Azure Stack. Balíčky těchto aktualizací můžete stáhnout přímo od Microsoftu.

- **Výrobce OEM aktualizací poskytnutých dodavatelem hardwaru**. Partneři hardware Azure Stack je zodpovědná za začátku do konce údržby životní cyklus (včetně pokynů) pro související s hardwarem firmware a aktualizace balíčků ovladačů. Kromě toho partneři hardware Azure Stack vlastní a Udržovat pokyny pro veškerý software a hardware na hostitelský hardware životního cyklu. Výrobce OEM dodavatele hardwaru hostitelem tyto balíčky na vlastní web pro stahování aktualizací.


## <a name="update-package-release-cadence"></a>Aktualizace balíčku vydávání verzí
Microsoft se očekává, že verze balíčků aktualizací softwaru v měsíčním tempo. Je však možné mít více nebo žádná verze aktualizace v daném měsíci. Výrobce OEM výrobci hardwaru vydávat jejich aktualizace na základě podle potřeby. 

Vyhledejte si dokumentaci na tom, jak naplánovat a spravovat aktualizace a jak určit vaší aktuální verzí v [spravovat aktualizace přehled](azure-stack-updates.md). 

Informace o konkrétní aktualizaci včetně si ho stáhnout, naleznete v tématu poznámky k verzi pro, které aktualizace: 
- [Aktualizace služby Azure Stack 1809](azure-stack-update-1809.md)
- [Aktualizace služby Azure Stack. 1808](azure-stack-update-1808.md)
- [Aktualizace služby Azure Stack 1807](azure-stack-update-1807.md)

## <a name="hotfixes"></a>Opravy hotfix
V některých případech společnost Microsoft poskytuje opravy hotfix pro Azure Stack, které řeší konkrétní problém, který je často preventivní nebo časovým počitadlem.  Každý opravy hotfix jsou vydány s odpovídající článek znalostní báze Microsoft s podrobnostmi o problému, příčině a řešení. 

Opravy hotfix se stahují a instalují stejně jako regulární úplnou aktualizaci balíčků pro službu Azure Stack. Ale na rozdíl od úplné aktualizace, opravy hotfix můžete nainstalovat během několika minut. Doporučujeme, abyste že operátorům Azure stacku nastavení časového období údržby při instalaci oprav hotfix. Opravy hotfix aktualizujte verzi cloudu služby Azure Stack, můžete snadno zjistit, pokud byl použit opravu hotfix. Samostatné opravy hotfix se poskytuje pro každou verzi služby Azure Stack, která se stále podpory. Jednotlivé opravy pro konkrétní iteraci je kumulativní a obsahuje předchozí aktualizace pro stejnou verzi. Další informace o použitelnosti konkrétního oprava hotfix v opravy odpovídající znalostní báze Knowledge Base article.  


## <a name="keep-your-system-under-support"></a>Zachovat systému v rámci podpory
Pokračujte k získání podpory, je nutné zachovat vašeho nasazení Azure stacku aktuální. Zásady odložení aktualizace je: Pro nasazení Azure Stack tak si zachováte podpory se musí spustit nedávno vydané verze aktualizace nebo spustit některý z dvě předchozí verze aktualizace. Opravy hotfix, se nepovažují aktualizace hlavní verze. Pokud cloudu služby Azure Stack je za *více než dvě aktualizace*, je to považovat za nedodržení a musíte aktualizovat alespoň minimální podporovaná verze získání podpory. 

Například pokud nejvíce dostupného aktualizovanou verzi je 1805 a předchozí dva balíčky aktualizace byly verze 1804 a 1803, 1803 a 1804 zůstanou na podporu. Je však 1802 bez podpory. Zásady platí, pokud neexistuje žádná verze pro měsíc nebo dvě. Například pokud v aktuální verzi je 1805 a nebyly žádné verzi 1804, předchozí dva balíčky aktualizací 1803 a 1802 zůstanou na podporu.

Balíčky aktualizací softwaru společnosti Microsoft jsou mimo kumulativní a vyžadovat předchozí aktualizace balíčku nebo opravu hotfix jako předpoklad. Pokud se rozhodnete odložení jeden nebo více aktualizací, zvažte celkové modulu runtime, pokud chcete získat nejnovější verzi. 

## <a name="get-support"></a>Získat podporu
Azure Stack se řídí podporu stejně jako Azure. Podnikoví zákazníci můžou postupujte podle procesu popsaného v [postupy vytvoření žádosti o podporu Azure](/azure/azure-supportability/how-to-create-azure-support-request). Pokud jste zákazník z Cloud Service Provider (CSP), požádejte o podporu poskytovatel cloudových služeb.  Další informace najdete v tématu [nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Další postup

- [Správa aktualizací ve službě Azure Stack](azure-stack-updates.md)


