---
Description: md5 useage
Keywords:
- Development
- md5
Tags:
- md5
Title: md5 golang example
Topics:
- md5
date: 2017-01-10
---


```
package main

import (
	"crypto/md5"
	"flag"
	"fmt"
	"io/ioutil"
	"os"
	"path/filepath"
	"sort"
)

var directory, file *string

func Md5SumFolder(folder string) (map[string][md5.Size]byte, error) {
	result := make(map[string][md5.Size]byte)
	err := filepath.Walk(folder, func(path string, info os.FileInfo, err error) error {
		if err != nil {
			return err
		}
		if !info.Mode().IsRegular() {
			return nil
		}
		data, err := ioutil.ReadFile(path)
		if err != nil {
			return err
		}
		result[path] = md5.Sum(data)
		return nil
	})
	if err != nil {
		return nil, err
	}
	return result, nil
}

func Md5SumFile(file string) (value [md5.Size]byte, err error) {
	data, err := ioutil.ReadFile(file)
	if err != nil {
		return
	}
	value = md5.Sum(data)
	return
}

func init() {
	directory = flag.String("d", "", "The directory contains all the files that need to alculate the md5 value")
	file = flag.String("f", "", "The file that need to caclulate the md5 value")
}

func main() {
	flag.Parse()
	if *directory == "" && *file == "" {
		flag.Usage()
		return
	}
	if *file != "" {
		md5Value, err := Md5SumFile(*file)
		if err != nil {
			fmt.Println(err.Error())
			return
		}
		fmt.Printf("%x %s\n", md5Value, *file)
		return
	}
	if *directory != "" {
		result, err := Md5SumFolder(*directory)
		if err != nil {
			fmt.Println(err.Error())
			return
		}
		var paths []string
		for path := range result {
			paths = append(paths, path)
		}
		sort.Strings(paths)
		for _, path := range paths {
			fmt.Printf("%x %s\n", result[path], path)
		}
	}
}

```
