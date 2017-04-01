#!/usr/bin/env python

import os
import re
import struct
import sys
from subprocess import *

if len(sys.argv) < 2:
	print "Usage: "+sys.argv[0]+": file [file ..]"
	sys.exit(1)

def extract(arg):
	# build output file name
	outputName = os.path.splitext(arg)[0] + "_df.pct"
	p = Popen(["DeRez", "-only", "PICT", arg], stdout=PIPE, stderr=PIPE)
	out, err = p.communicate()
	out = out.split('\n')
	for l in out:
		if len(l) == 1:
			continue
		# start of file
		if re.match('data \'PICT\'', l):
			m = re.search('\(\d*\)', l)
			num = m.group(0)
			num = num.translate(None, '()')
			working = open(outputName, "wb")
			print "\"%s\" > \"%s\"" % (arg, outputName)
			for a in range(0, 512):
				s = struct.pack(">B", 0);
				working.write(s)
		# end of file
		elif re.match('\}\;', l):
			working.close()
		# file data
		else:
			# avoid errors trying to parse comments
			if len(l) > 48:
				l = l[:48]
			cap = re.search('\".*\" ', l)
			if cap is None:
				continue
			data = cap.group(0)
			data = data.translate(None, '"')
			for a in data.split(' '):
				if len(a) != 4:
					continue
				first, second = a[:len(a)/2], a[len(a)/2:]
				# print "first=%s, second=%s" % (first, second)
				pdata = struct.pack(">BB", int(first, 16), int(second, 16))
				working.write(pdata)

for arg in sys.argv[1:]:
	extract(arg)
