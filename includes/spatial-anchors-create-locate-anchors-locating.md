## <a name="locating-a-cloud-spatial-anchor"></a>Vyhledání prostorových ukotvení cloudu

Vyhledat prostorové Cloudová ukotvení, budete potřebovat znát jejich identifikátorů. ID ukotvení, může být uložené ve službě back-endu vaší aplikace a je dostupná pro všechna zařízení, které lze k němu správné ověření. Příklad najdete v článku [kurzu: Sdílet prostorových kotvy napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvořit instanci objektu AnchorLocateCriteria, nastavte identifikátory, které hledáte a tím, že poskytuje vaše AnchorLocateCriteria vyvolat metodu CreateWatcher v relaci.
