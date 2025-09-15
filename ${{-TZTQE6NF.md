---
tags: []
parent: ""
collections: []
$version: 3728
$libraryID: 1
$itemKey: TZTQE6NF

---
\${{ // 初始化颜色分类 //<https://github.com/windingwind/zotero-better-notes/discussions/1358> //by Vicissitude-com sharedObj.colors = { "#ffd400": { name: "📚目的", annotations: \[] }, // Yellow "#ff6666": { name: "💡内容", annotations: \[] }, // Red "#5fb236": { name: "🛠️创新点、难点", annotations: \[] }, // Green "#2ea8e5": { name: "📊关注点", annotations: \[] }, // Blue "#a28ae5": { name: "🚧参考文献", annotations: \[] }, // Purple "#e56eee": { name: "⚠️生词", annotations: \[] }, // Magenta "#f19837": { name: "💭复现", annotations: \[] }, // Orange "#aaaaaa": { name: "🗂️其他", annotations: \[] } // Grey };

```
sharedObj.itemHeaders = new Set();

// 提取文献信息
const date = topItem.getField("date") ? topItem.getField("date").split('T')[0] : '';
const title = topItem.getField("title") || '';
const titleTranslation = topItem.getField("titleTranslation") || '';
const creators = topItem.getCreators().slice(0, 10).map((v) => `${v.firstName} ${v.lastName}`).join("; ") + (topItem.getCreators().length > 10 ? "; et al." : "");
const publicationTitle = topItem.getField('publicationTitle') || '';
const volume = topItem.getField('volume') || '';
const issue = topItem.getField('issue') || '';
const pages = topItem.getField('pages') || '';
const year = topItem.getField('date') ? topItem.getField('date').split('-')[0] : '';
const itemHeader = `<i>${title}</i>`;
const itemHeaderWithDate = date ? `(${date}) ${itemHeader}` : itemHeader;
const publicationInfo = `${publicationTitle}${volume ? `, ${volume}` : ''}${issue ? `(${issue})` : ''}${pages ? `: ${pages}` : ''}${year ? `, ${year}` : ''}.`;

// 将文献标题添加到集合中
sharedObj.itemHeaders.add(itemHeader);

// 输出文献信息
const literatureInfo = `
    <h1 style="color:#193c47; background-color:#eef9fd; padding:8px;">
        ${itemHeaderWithDate}
    </h1>
    <table>
        <tr><td style="color:#193c47; background-color:#dbeedd; padding:8px;"><b>作者:</b> ${creators}</td></tr>
        <tr><td style="color:#193c47; background-color:#f3faf4; padding:8px;"><b>期刊:</b> <b style="color:#FF0000">${publicationInfo}</b></td></tr>
        <tr><td style="color:#193c47; background-color:#dbeedd; padding:8px;"><b>期刊分区:</b> ${Array.prototype.map.call(Zotero.ZoteroStyle.api.renderCell(topItem, "publicationTags").childNodes, e => {
            e.innerText = " ㅤㅤ ㅤㅤ " + e.innerText + " ㅤㅤ ㅤㅤ ";
            return e.outerHTML
        }).join(" ㅤㅤ ㅤㅤ ")}</td></tr>
        <tr><td style="color:#193c47; background-color:#f3faf4; padding:8px;">${(() => {
            const attachments = Zotero.Items.get(topItem.getAttachments());
            const pdf = attachments.filter((i) => i.isPDFAttachment());
            if (pdf && pdf.length > 0) {
                return `<b>本地链接:</b> <a href="zotero://open-pdf/0_${pdf[0].key}">${pdf[0].getFilename()}</a>`;
            } else if (attachments && attachments.length > 0) {
                return `<b>本地链接:</b> <a href="zotero://open-pdf/0_${attachments[0].key}">${attachments[0].getFilename()}</a>`;
            } else {
                return `<b>本地链接:</b>`;
            }
        })()}</td></tr>
        <tr><td style="color:#193c47; background-color:#dbeedd; padding:8px;">${(() => {
            const doi = topItem.getField("DOI");
            if (doi) {
                return `<b>DOI:</b> <a href="https://doi.org/${topItem.getField('DOI')}">${topItem.getField('DOI')}</a>`;
            } else {
                return `<b>URL:</b> <a href="${topItem.getField('url')}">${topItem.getField('url')}</a>`;
            }
        })()}</td></tr>
        <tr><td style="color:#193c47; background-color:#f3faf4; padding:8px;">${(() => {
            const abstractTranslation = topItem.getField('abstractTranslation');
            if (abstractTranslation) {
                return `<b>摘要翻译:</b> <i>${abstractTranslation}</i>`;
            } else {
                return `<b>摘要:</b> <i>${topItem.getField('abstractNote')}</i>`;
            }
        })()}</td></tr>
        <tr><td style="color:#193c47; background-color:#dbeedd; padding:8px;"><b>标签:</b> ${topItem.getTags().map(tagObj => tagObj.tag.startsWith('#') ? tagObj.tag + ' ,':'').join(' ') || ''}</td></tr>
        <tr><td style="color:#193c47; background-color:#f3faf4; padding:8px;"><b>笔记日期:</b> ${new Date().toLocaleString()}</td></tr>
    </table>
`;

// 提取注释笔记
async function getAnnotationsByColor(_attachment, color) {
    const annots = _attachment.getAnnotations().filter((item) => item.annotationColor === color);
    if (annots.length === 0) {
        return "";
    }
    return Zotero.BetterNotes.api.convert.annotations2html(annots, {
        noteItem: targetNoteItem,
    });
}

const attachments = Zotero.Items.get(topItem.getAttachments()).filter((i) => i.isPDFAttachment() || i.isSnapshotAttachment() || i.isEPUBAttachment());

await Promise.all(Object.keys(sharedObj.colors).map(async (color) => {
    const annotationsPromises = attachments.map((attachment) => getAnnotationsByColor(attachment, color));
    const renderedAnnotationsArray = await Promise.all(annotationsPromises);
    const renderedAnnotations = renderedAnnotationsArray.filter(Boolean).join("");
    sharedObj.colors[color].annotations.push(`<h3>${itemHeader}</h3>`, renderedAnnotations);
}));

// 输出注释笔记
let annotationsOutput = "";
for (const color in sharedObj.colors) {
    const colorData = sharedObj.colors[color];
    if (colorData.annotations.length > 0) {
        const filteredAnnotations = colorData.annotations.filter(annotation => annotation !== `<h3>${itemHeader}</h3>`).join("");
        annotationsOutput += `
            <h2 style="background-color:${color}70; padding: 8px; margin: 0;">
                ${colorData.name}
            </h2>
            ${filteredAnnotations}
        `;
    }
}

return literatureInfo + annotationsOutput;
```

}}\$
