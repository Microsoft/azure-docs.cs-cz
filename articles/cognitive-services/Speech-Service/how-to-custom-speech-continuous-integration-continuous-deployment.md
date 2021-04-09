---
title: CI/CD pro Custom Speech službu pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Použijte DevOps s pracovními postupy pro Custom Speech a CI/CD. Implementujte existující řešení DevOps pro váš vlastní projekt.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f7f11ef097d3abee2b4c18c32a1cb215d9fe8ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939900"
---
# <a name="cicd-for-custom-speech"></a>CI/CD pro Custom Speech

Implementací automatizovaného školení, testování a správy vydaných verzí umožníte průběžné vylepšování Custom Speech modelů při použití aktualizací pro školení a testování dat. Díky efektivní implementaci pracovních postupů CI/CD můžete zajistit, aby byl koncový bod pro nejlepší Custom Speech model vždy k dispozici.

[Průběžná integrace](/azure/devops/learn/what-is-continuous-integration) (CI) je technickým postupem, který často potvrzování aktualizací ve sdíleném úložišti a provádí automatizované sestavení. Pracovní postupy CI pro Custom Speech provedou nový model z jeho zdrojů dat a provádějí automatizované testování nového modelu, aby bylo zajištěno lepší zlepšení než předchozí model.

[Průběžné doručování](/azure/devops/learn/what-is-continuous-delivery) (CD) přebírá modely z procesu ci a vytvoří koncový bod pro každý vylepšený model Custom Speech. Díky CD jsou koncové body snadno dostupné pro integraci do řešení.

Vlastní řešení CI/CD jsou možná, ale pro robustní předem připravené řešení použijte [úložiště šablon pro rozpoznávání řeči DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template), které spouští pracovní postupy CI/CD pomocí akcí GitHubu.

## <a name="cicd-workflows-for-custom-speech"></a>Pracovní postupy CI/CD pro Custom Speech

Účelem těchto pracovních postupů je zajistit, aby každý model Custom Speech měl lepší přesnost rozpoznávání než předchozí sestavení. Pokud aktualizace testování a/nebo školicích dat zlepšují přesnost, tyto pracovní postupy vytvoří nový koncový bod Custom Speech.

Servery Git, jako je GitHub a Azure DevOps, můžou spouštět automatizované pracovní postupy, když dojde ke konkrétním událostem Git, jako jsou například sloučení nebo žádosti o přijetí změn. Pracovní postup CI lze například aktivovat při vložení aktualizací k testování dat do *Hlavní* větve. Různé servery Git budou mít různé nástroje, ale budou umožňovat skriptování příkazů rozhraní příkazového řádku (CLI), aby je bylo možné spustit na serveru sestavení.

Pracovní postupy by pak měly pojmenovat a ukládat data, testy, soubory testů, modely a koncové body, aby je bylo možné trasovat zpět na potvrzení nebo verzi, ze které pocházejí. Je také užitečné tyto prostředky pojmenovat tak, aby bylo snadné zjistit, které byly vytvořeny po aktualizaci testovacích dat a školicích dat.

### <a name="ci-workflow-for-testing-data-updates"></a>Pracovní postup CI pro testování aktualizací dat

Hlavním účelem pracovních postupů CI/CD je vytvořit nový model pomocí školicích dat a otestovat tento model pomocí testovacích dat, aby se zjistilo, jestli se v porovnání s předchozím modelem nejlepšího provádění ("srovnávací model") zlepšila [míra chyb slov](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER). Pokud nový model využije lepší, stává se novým modelem srovnávacího testu, proti kterému jsou porovnávány budoucí modely.

Pracovní postup CI pro testování aktualizací dat by měl znovu otestovat aktuální srovnávací model s aktualizovanými testovacími daty za účelem výpočtu revidované služby WER. Tím je zajištěno, že když se WER nového modelu porovná se zákrokem srovnávacího testu, oba modely byly testovány proti stejným testovacím datům a porovnáváte se podobně jako.

Tento pracovní postup by měl aktivovat aktualizace pro testování dat a:

- Otestujte model srovnávacích testů proti aktualizovaným testovacím datům.
- Uložte výstup testu, který obsahuje WER modelu srovnávacího testu s použitím aktualizovaných dat.
- Do služby WER z těchto testů se stane nová srovnávací akce WER, kterou by budoucí modely musely přeniknout.
- Pracovní postup CD nespustí aktualizace pro testování dat.

### <a name="ci-workflow-for-training-data-updates"></a>Pracovní postup CI pro školicí aktualizace dat

Aktualizace pro školení dat značí aktualizace vlastního modelu.

Tento pracovní postup by měl aktivovat aktualizace pro školení dat a:

- Výuka nového modelu s aktualizovanými školicími daty
- Otestujte nový model s testovacími daty.
- Uložte výstup testu, který obsahuje WER.
- Porovnejte WER z nového modelu do služby WER z modelu srovnávacích testů.
- Pokud se WER nevylepšuje, zastavte pracovní postup.
- Pokud se služba WER vylepšuje, spusťte pracovní postup CD a vytvořte Custom Speech koncový bod.

### <a name="cd-workflow"></a>Pracovní postup CD

Když aktualizace školicích dat vylepšuje rozpoznávání modelu, pracovní postup CD by se automaticky měl spustit, aby vytvořil nový koncový bod pro tento model a aby tento koncový bod byl dostupný tak, aby se mohl použít v řešení.

#### <a name="release-management"></a>Správa vydaných verzí

Většina týmů vyžaduje ruční kontrolu a proces schvalování pro nasazení do produkčního prostředí. V případě produkčního nasazení si můžete být jisti, že k němu dojde, když jsou klíčová lidé ve vývojovém týmu k dispozici pro podporu nebo v obdobích s nízkým provozem.

### <a name="tools-for-custom-speech-workflows"></a>Nástroje pro Custom Speech pracovní postupy

K dispozici jsou následující nástroje pro postupy CI/CD Automation pro Custom Speech:

- [Azure CLI](/cli/azure/) pro vytvoření ověřování instančního objektu Azure, dotazování předplatných Azure a uložení výsledků testů v objektu blob Azure.
- [Azure Speech CLI](spx-overview.md) pro interakci se službou Speech z příkazového řádku nebo automatizovaného pracovního postupu.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>Řešení DevOps pro Custom Speech s využitím akcí GitHubu

Pro už implementované řešení DevOps pro Custom Speech se můžete dostat do [úložiště šablon řeči DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template). Vytvořte kopii šablony a začněte vyvíjet vlastní modely pomocí robustního DevOps systému, který zahrnuje testování, školení a správu verzí pomocí akcí GitHubu. Úložiště poskytuje ukázkové testování a školicí data pro pomoc při instalaci a vysvětluje pracovní postup. Po počátečním nastavení nahraďte vzorová data daty projektu.

[Úložiště šablon pro rozpoznávání řeči DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) poskytuje infrastrukturu a podrobné pokyny k těmto akcím:

- Zkopírujte úložiště šablon do svého účtu GitHubu a pak vytvořte prostředky Azure a [instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) pro pracovní postupy CI/CD akcí GitHubu.
- Projděte si "[vnitřní smyčka pro vývoj](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)". Aktualizace školení a testování dat z větve funkcí, testování změn pomocí dočasného vývojového modelu a získání žádosti o přijetí změn pro návrh a kontrolu změn.
- V případě, že se školicí data v žádosti o přijetí změn aktualizují na *Hlavní*, provedou se modely s pracovními postupy pro akce GitHubu.
- Proveďte automatizované testování přesnosti, aby se navázala [míra chyb ve wordovém](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) modelu (WER). Výsledky testů uložte do objektu blob Azure.
- Spusťte pracovní postup CD pro vytvoření koncového bodu, když se služba WER vylepšuje.

## <a name="next-steps"></a>Další kroky

Další informace o DevOps pomocí řeči:

- Použijte [úložiště šablon DevOps řeči](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) k implementaci DevOps pro Custom Speech s akcemi GitHubu.
