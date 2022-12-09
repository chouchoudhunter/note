# 🚀️ 瞅瞅笔记

记录我的所有学习的笔记 😄

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

```dataviewjs  
const pages = dv.pages('"前端开发"')
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
	type:'doughnut',
	data:{
		labels: labels,
		datasets: [{
		    label: 'My First Dataset',
		    data: numbers,
			backgroundColor: [
				'rgb(255, 99, 132)',
				'rgb(54, 162, 235)',
				'rgb(54, 162, 35)',
				'rgb(255, 205, 86)'
			],
			hoverOffset: 4
		}]
	}
};
var temp=document.createElement("div")
temp.style="display:flex"
var block1=document.createElement("div")
block1.style="width:50%"
var block2=document.createElement("div")
block2.style="width:50%"
window.renderChart(chartData, block1);
window.renderChart(chartData, block2);
temp.append(block1)
temp.append(block2)
this.container.append(temp)
```