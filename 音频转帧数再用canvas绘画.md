## 首先，音频转帧数要在服务器上打开 ##

#### 1，从音频源获取数据，你需要一个 AnalyserNode节点，它可以用 AudioContext.createAnalyser() 方法创建 ####

	var audioCtx = new (window.AudioContext || window.webkitAudioContext)();
	var analyser = audioCtx.createAnalyser();

#### 把这个节点连接到你的声源 ####
	var videoNode = document.getElementById("videoNode");
	source = audioCtx.createMediaElementSource(videoNode);
	source.connect(analyser);
	analyser.connect(audioCtx.destination);

#### 设置ffiSize ####

	analyser.fftSize = 64;
	var bufferLength = analyser.fftSize;
	var dataArray = new Uint8Array(bufferLength);//这里也可以自己定义长度（30等）

#### 绘画的时候 ####


	var wrap = document.getElementById("myCanvas")
	var canvasCtx = wrap.getContext("2d");
    canvasCtx.fillStyle = "#ededee";
    canvasCtx.strokeStyle = "#ededee";
    // canvasCtx.lineWidth = 1;

	canvasCtx.clearRect(0, 0, 500, 500);

	function draw(){
		  drawVisual = requestAnimationFrame(draw);
		  analyser.getByteTimeDomainData(dataArray);

			//这下面是canvas绘画步骤
		  canvasCtx.fillStyle = 'rgb(200, 200, 200)';
	      canvasCtx.fillRect(0, 0, 500, 500);
	      canvasCtx.lineWidth = 1;
	      canvasCtx.strokeStyle = 'rgb(0, 0, 0)';
	
	      canvasCtx.beginPath();
	      var sliceWidth = 500 * 1.0 / bufferLength;
	      for(var i = 0; i < bufferLength; i++) {
	   		
	        var v = (dataArray[i]) / 128.0 + 2;
	        var y = v * 200/2;
	       	var x = i*20+50;
	       	canvasCtx.moveTo(x,dataArray[i]*2);
	        canvasCtx.lineTo(x,y);
	      }
	      canvasCtx.stroke();
	}

	draw();