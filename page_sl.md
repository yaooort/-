```js
/**
 * 对页组进行分页
 * @param page 当前页
 * @param pages 总页数
 * @param show_page_num 一页多少条数据 尽量使用奇数。中间对称
 * @returns {Array} 返回页组
 */
function goPage(page, pages, show_page_num) {
    let page_arr = [];//返回页组
    if (pages > 0 && page <= pages) {
        page_arr.push(1);
        let sa = Math.floor(show_page_num / 2);//分割页数。使
        let start = page > sa ? ((page - sa) === 1 ? 2 : page + sa > pages ? pages - 1 : page - sa) : 2 > pages ? pages : 2;
        let end = start + show_page_num >= pages ? pages : start + show_page_num;
        for (let i = start; i < end; i++) {
            page_arr.push(i)
        }
        if (pages !== 1) {
            page_arr.push(pages);
        }
    }
    console.log(JSON.stringify(page_arr))
    return page_arr;
}
```
