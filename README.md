## go-mxnet-predictor

[![Build Status](https://travis-ci.org/songtianyi/go-mxnet-predictor.svg?branch=master)](https://travis-ci.org/songtianyi/go-mxnet-predictor)
[![Go Report Card](https://goreportcard.com/badge/github.com/songtianyi/go-mxnet-predictor)](https://goreportcard.com/report/github.com/songtianyi/go-mxnet-predictor)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)


go-mxnet-predictor is go binding for mxnet c_predict_api. It's almost as raw as original C api, wish further development for higher level APIs.


## Steps to get your own dev environment
###### Get mxnet and build
	mkdir /root/MXNet/
	cd /root/MXNet/ && git clone https://github.com/dmlc/mxnet.git --recursive
	cd /root/MXNet/mxnet && make -j2
	ln -s $MXNET/mxnet/lib/libmxnet.so /usr/lib/libmxnet.so

###### Get go-mxnet-predictor
	go get github.com/anthonynsimon/bild
    go get -u -v github.com/songtianyi/go-mxnet-predictor

## Steps to build flower example
###### Get model files and input image

###### Build predict.go
	go build examples/flowers/predicto.go

## Steps to do inference
###### 1. Load pre-trained model and Create go predictor
	p, err := mxnet.CreatePredictor(symbol, params, mxnet.Device{mxnet.CPU_DEVICE, 0}, []mxnet.InputNode{{Key: "data", Shape: []uint32{1, 3, 299, 299}}})
	if err != nil {
		panic(err)
	}
	defer p.Free()
	// see more details in examples/flowers/predict.go

###### 2. Load input data and do preprocess
	// load test image for predction
	img, err := imgio.Open("/data/flowertest.jpg")
	if err != nil {
		panic(err)
	}
	// preprocess
	resized := transform.Resize(img, 299, 299, transform.Linear)
	res, err := utils.CvtImageTo1DArray(resized, item.Data)
	if err != nil {
		panic(err)
	}

###### 3. Set input data to preditor
	// set input
	if err := p.SetInput("data", res); err != nil {
		panic(err)
	}
###### 4. Do predict
	// do predict
	if err := p.Forward(); err != nil {
		panic(err)
	}

###### 5. get prediction data
	// get predict result
	data, err := p.GetOutput(0)
	if err != nil {
		panic(err)
	}
	// see more details in examples/flowers/predict.go


