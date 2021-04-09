---
title: Vlastnosti metadat obsahu
titleSuffix: Azure Cognitive Search
description: Vlastnosti metadat dokumentu můžou poskytovat obsah do polí indexu vyhledávání nebo informace, které v době běhu informují chování při indexování. Tento článek obsahuje seznam vlastností metadat podporovaných v Azure Kognitivní hledání.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668414"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Vlastnosti metadat obsahu používané v Azure Kognitivní hledání

SharePoint Online a Azure Blob Storage můžou obsahovat různé obsahy a mnohé z těchto typů obsahu mají vlastnosti metadat, které mohou být užitečné pro index. Stejně jako můžete vytvořit vyhledávací pole pro standardní vlastnosti objektů blob, jako je **`metadata_storage_name`** , můžete vytvořit pole pro vlastnosti metadat, které jsou specifické pro formát dokumentu.

## <a name="supported-document-formats"></a>Podporované formáty dokumentů

Kognitivní hledání podporuje indexování objektů BLOB a indexování dokumentů SharePointu Online pro následující formáty dokumentů:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Vlastnosti podle formátu dokumentu

Následující tabulka shrnuje zpracování pro jednotlivé formáty dokumentů a popisuje vlastnosti metadat extrahované indexerem objektů BLOB a indexerem služby SharePoint Online.

| Formát dokumentu/typ obsahu | Extrahovaná metadata | Podrobnosti zpracování |
| --- | --- | --- |
| HTML (text/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Obložení kódu HTML a extrakce textu |
| PDF (aplikace/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů (s výjimkou obrázků) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| DOC (Application/MSWord) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Obložení kódu XML a extrakce textu |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Obložení kódu XML a extrakce textu |
| XLSX (application/vnd. openxmlformats-officedocument. SpreadsheetML. list) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| XLSM (application/vnd. MS-Excel. list. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| PPTX (application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahujte text, včetně textu extrahovaného z příloh. `metadata_message_to_email``metadata_message_cc_email`a `metadata_message_bcc_email` jsou kolekce řetězců, zbývající pole jsou řetězce.|
| ODT (application/vnd. Oasis. OpenDocument. text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| ODS (application/vnd. Oasis. OpenDocument. Spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| ODP (application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrakce textu, včetně vložených dokumentů |
| ZIP (aplikace/ZIP) |`metadata_content_type` |Extrakce textu ze všech dokumentů v archivu |
| GZ (Application/gzip) |`metadata_content_type` |Extrakce textu ze všech dokumentů v archivu |
| EPUB (Application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrakce textu ze všech dokumentů v archivu |
| XML (Application/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Obložení kódu XML a extrakce textu |
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrakce textu<br/>Poznámka: Pokud potřebujete extrahovat více polí dokumentů z objektu BLOB JSON, přečtěte si podrobnosti v tématu [indexování objektů BLOB JSON](search-howto-index-json-blobs.md) . |
| EML (zpráva/RFC822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrakce textu, včetně příloh |
| RTF (aplikace/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrakce textu|
| Prostý text (text/prostý) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrakce textu|

## <a name="see-also"></a>Viz také

* [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
* [Principy objektů BLOB pomocí AI](search-blob-ai-integration.md)
* [Přehled indexování objektů BLOB](search-blob-storage-integration.md)
* [Indexování SharePointu Online](search-howto-index-sharepoint-online.md)