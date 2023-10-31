# 🚀️ 瞅瞅笔记

记录我的所有学习的笔记。 😄

# 技能
```dataviewjs

function getChartData(url='前端开发'){
	const pages = dv.pages("\""+url+"\"")
	const labels=[]
	const numbers=[]
	console.log(pages)
	for(let e of pages){
		e=e.file
		let label=e.folder.split('/')[1]
		let haveLabel=false
		for(let i=0;i<labels.length;i++){
			if(labels[i]==label){
				haveLabel=true;
				numbers[i]++
				break
			}
		}
		if(!haveLabel){
			labels[labels.length]=label
			numbers[numbers.length]=1
		}
	}
	const chartData = {
		type:'bar',
		data:{
			labels: labels,
			datasets: [{
			    label: url,
			    data: numbers,
				backgroundColor: [
					'rgba(255, 99, 132, 0.2)',
					'rgba(255, 159, 64, 0.2)',
					'rgba(255, 205, 86, 0.2)',
					'rgba(75, 192, 192, 0.2)',
					'rgba(54, 162, 235, 0.2)',
					'rgba(153, 102, 255, 0.2)',
					'rgba(201, 203, 207, 0.2)'
				],
				borderColor: [
					'rgb(255, 99, 132)',
					'rgb(255, 159, 64)',
					'rgb(255, 205, 86)',
					'rgb(75, 192, 192)',
					'rgb(54, 162, 235)',
					'rgb(153, 102, 255)',
					'rgb(201, 203, 207)'
				],
			    borderWidth: 1,
			}]
		}
	};
	return chartData
}
var temp=document.createElement("div")
temp.style="display:flex"
var block1=document.createElement("div")
block1.style="width:50%"
var block2=document.createElement("div")
block2.style="width:50%"

var chardata1=getChartData()
var chardata2=getChartData('后端开发')
window.renderChart(chardata1, block1);
window.renderChart(chardata2, block2);
temp.append(block1)
temp.append(block2)
this.container.append(temp)
```
# 我的
```dataviewjs  
const pages = dv.pages('"我的"')
const labels=[]
const numbers=[]
console.log(pages)
for(let e of pages){
	e=e.file
	let label=e.folder.split('/')[1]
	let haveLabel=false
	for(let i=0;i<labels.length;i++){
		if(labels[i]==label){
			haveLabel=true;
			numbers[i]++
			break
		}
	}
	if(!haveLabel){
		labels[labels.length]=label
		numbers[numbers.length]=1
	}
}
const chartData = {
	type:'bar',
	data:{
		labels: labels,
		datasets: [{
            label: '书籍',
            data: [65, 59, 80, 81, 56, 55, 40],
            borderColor: 'rgb(75, 192, 192)',
            backgroundColor:'rgba(255, 99, 132, 0.5)',
            stack:'group1'
        },
        {
            label: '游戏',
            data: [85, 79, 100, 101, 76, 75, 60],
            borderColor: 'rgb(175, 92, 92)',
            backgroundColor:'rgba(153, 102, 255, 0.5)'
        },        
        {
            label: '影视',
            data: [185, 179, 200, 201, 176, 175, 160],
            borderColor: 'rgb(125, 42, 192)',
            backgroundColor:'rgba(255, 205, 86, 0.5)'
        },        
        {
            label: '日记',
            data: [185, 179, 200, 201, 176, 175, 160],
            borderColor: 'rgb(125, 42, 192)',
            backgroundColor:'rgba(255, 205, 86, 0.5)'
        }]
	}
};
var temp=document.createElement("div")
var block1=document.createElement("div")
window.renderChart(chartData, block1);
temp.append(block1)
this.container.append(temp)
```